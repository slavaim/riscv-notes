
A manual stack unwinding when ```handle_exception``` sets a call frame

```
(gdb) bt
#0  0xffffffff8002e0c4 in _panic (caller=0xffffffff80002554 <do_trap_insn_misaligned+52>, frame=0xffffffff80040de8 <init_thread_union+7656>, fmt=0xffffffff800343c8 "%s unimplemented\n")
    at kernel/lib/debug/debug.c:32
#1  0xffffffff80002490 in do_trap_error (regs=0xffffffff80040e08 <init_thread_union+7688>, signo=7, code=1, addr=18446744071562076884, str=0xffffffff80034420 "Oops - instruction address misaligned")
    at kernel/arch/riscv/traps.c:52
#2  0xffffffff80002554 in do_trap_insn_misaligned (regs=0xffffffff80040e08 <init_thread_union+7688>) at kernel/arch/riscv/traps.c:67
#3  0xffffffff8000027c in handle_exception () at kernel/arch/riscv/rv64/exception.S:221
Backtrace stopped: frame did not save the PC
```

A frame stack has the following layout
```
struct frame{
    void* caller_pc;
    coid* caller_s0;
}
```
the current frame pointer is saved in the ```s0``` register.

```
(gdb) p/x $s0
$1 = 0xffffffff80040d88
(gdb) x/2xg 0xffffffff80040d88-16
0xffffffff80040d78 <init_thread_union+7544>:	0xffffffff80040de8	0xffffffff80002490
(gdb) x/2xg 0xffffffff80040de8-16
0xffffffff80040dd8 <init_thread_union+7640>:	0xffffffff80040e08	0xffffffff80002554
(gdb) x/2xg 0xffffffff80040e08-16
0xffffffff80040df8 <init_thread_union+7672>:	0xffffffff80040f30	0xffffffff8000027c
(gdb) x/2xg 0xffffffff80040f30-16
0xffffffff80040f20 <init_thread_union+7968>:	0xffffffff80040f70	0xffffffff800022d4
(gdb) x/2xg 0xffffffff80040f70-16
0xffffffff80040f60 <init_thread_union+8032>:	0xffffffff80040fb0	0xffffffff80008f20
(gdb) x/2xg 0xffffffff80040fb0-16
0xffffffff80040fa0 <init_thread_union+8096>:	0xffffffff80040fe0	0xffffffff80008ff8
(gdb) x/5i 0xffffffff8000027c
   0xffffffff8000027c <handle_exception+352>:	ld	s1,256(sp)
   0xffffffff80000280 <handle_exception+356>:	csrci	sstatus,2
   0xffffffff80000284 <handle_exception+360>:	andi	s1,s1,256
   0xffffffff80000288 <handle_exception+364>:	bnez	s1,0xffffffff80000294 <handle_exception+376>
   0xffffffff8000028c <handle_exception+368>:	addi	s1,sp,280
(gdb) x/5i 0xffffffff800022d4
   0xffffffff800022d4 <arch_thread_construct_first+36>:	sw	a5,0(a4)
   0xffffffff800022d8 <arch_thread_construct_first+40>:	jal	ra,0xffffffff800021e4 <current_thread_info>
   0xffffffff800022dc <arch_thread_construct_first+44>:	sd	a0,-40(s0)
   0xffffffff800022e0 <arch_thread_construct_first+48>:	ld	a5,-40(s0)
   0xffffffff800022e4 <arch_thread_construct_first+52>:	ld	a4,-56(s0)
(gdb) x/5i 0xffffffff80008f20
   0xffffffff80008f20 <thread_construct_first+188>:	addi	a5,s0,-48
   0xffffffff80008f24 <thread_construct_first+192>:	li	a2,0
   0xffffffff80008f28 <thread_construct_first+196>:	mv	a1,a5
   0xffffffff80008f2c <thread_construct_first+200>:	lui	a5,0x80045
   0xffffffff80008f30 <thread_construct_first+204>:	addi	a0,a5,-1232
(gdb) x/5i 0xffffffff80008ff8
   0xffffffff80008ff8 <thread_init_early+112>:	jal	ra,0xffffffff80007678 <sched_init_early>
   0xffffffff80008ffc <thread_init_early+116>:	nop
   0xffffffff80009000 <thread_init_early+120>:	ld	ra,40(sp)
   0xffffffff80009004 <thread_init_early+124>:	ld	s0,32(sp)
   0xffffffff80009008 <thread_init_early+128>:	ld	s1,24(sp)
```
  
GDB can not unwind after ```handle_exception``` as it is isunable to verify that a frame pointer is valid, the ```handle_exception``` function has been written on assembler with a prolog that restores ```sp``` from a scratch register instead of a frame initalization. I added a frame pointer saving for ```handle_exception``` after the stack pointer restoration.  
  

```
    /*a call frame to facilitate with debugging*/
    .macro SET_GDB_FRAME
    addi	sp, sp, -2*SZREG /* allocate the frame */
    REG_S	s0, 0(sp)        /* get the frame pointer at the exception moment */
    csrr    s0, sepc         /* get the exception PC */
    REG_S	s0, SZREG(sp)    /* set the exception PC as $ra for the frame */
    addi	s0, sp, 2*SZREG  /* set s0 to the current frame pointer */
    .endm

    .macro DEL_GDB_FRAME
    REG_L	s0, 0(sp)        /* restore the caller frame pointer */
    addi	sp, sp, 2*SZREG  /* restore the stack pointer */
    .endm
```

Now the $pc, $sp and $s0 (a frame pointer ) can be set to unwind the stack before ```handle_exception``` was called by a CPU.

```
(gdb) set $pc=0xffffffff800022d4
(gdb) set $sp=0xffffffff80040f70
(gdb) set $s0=0xffffffff80040f70
(gdb) bt
#0  0xffffffff800022d4 in arch_thread_construct_first (t=0xffffffff800426d8 <idle_threads>) at kernel/arch/riscv/thread.c:34
#1  0xffffffff80008f20 in thread_construct_first (t=0xffffffff800426d8 <idle_threads>, name=0xffffffff80035368 "bootstrap") at kernel/kernel/thread.c:1016
#2  0xffffffff80008ff8 in thread_init_early () at kernel/kernel/thread.c:1037
#3  0xffffffff80003ec4 in lk_main () at kernel/top/main.c:53
#4  0xffffffff8000146c in _riscv_start () at kernel/arch/riscv/rv64/start.S:42
Backtrace stopped: frame did not save the PC
```
