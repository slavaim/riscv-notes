
A thread for a CPU is switched by a call to 

```
static inline void riscv_switch_to(struct riscv_thread_state * prev,
                                   struct riscv_thread_state * next)
{
    __switch_to_aux(prev, next);
    __switch_to(prev, next);
    assert(get_current());
}
```

The registers are saved and restored from the ```riscv_thread_state``` structure.

```
/*
 * Integer register context switch
 * The callee-saved registers must be saved and restored.
 * 
 *   a0: previous task_struct (must be preserved across the switch)
 *   a1: next task_struct
 */
.section .text
FUNCTION(__switch_to)
    /*
    * $a0 == &prev->arch.stat
    * $a1 == &next->arch.stat
    */
    /* Save context into prev->arch.state */
    REG_S ra,  THREAD_RA(a0)
    REG_S sp,  THREAD_SP(a0)
    REG_S s0,  THREAD_S0(a0)
    REG_S s1,  THREAD_S1(a0)
    REG_S s2,  THREAD_S2(a0)
    REG_S s3,  THREAD_S3(a0)
    REG_S s4,  THREAD_S4(a0)
    REG_S s5,  THREAD_S5(a0)
    REG_S s6,  THREAD_S6(a0)
    REG_S s7,  THREAD_S7(a0)
    REG_S s8,  THREAD_S8(a0)
    REG_S s9,  THREAD_S9(a0)
    REG_S s10, THREAD_S10(a0)
    REG_S s11, THREAD_S11(a0)
    /* Restore context from next->arch.state */
    REG_L ra,  THREAD_RA(a1)
    REG_L sp,  THREAD_SP(a1)
    REG_L s0,  THREAD_S0(a1)
    REG_L s1,  THREAD_S1(a1)
    REG_L s2,  THREAD_S2(a1)
    REG_L s3,  THREAD_S3(a1)
    REG_L s4,  THREAD_S4(a1)
    REG_L s5,  THREAD_S5(a1)
    REG_L s6,  THREAD_S6(a1)
    REG_L s7,  THREAD_S7(a1)
    REG_L s8,  THREAD_S8(a1)
    REG_L s9,  THREAD_S9(a1)
    REG_L s10, THREAD_S10(a1)
    REG_L s11, THREAD_S11(a1)
    REG_L tp,  THREAD_TI(a1) /* Next thread_info pointer */
    /*return to $ra, the new $sp has been set*/
    ret
END(__switch_to)
```

The new thread ```$ra``` points to the next instruction after a call to ```__switch_to``` inside ```riscv_switch_to```

```
(gdb) p/x newthread->arch.state
$137 = {ra = 0xffffffff80002f3c, sp = 0xffffffff8114cc80, s = {0xffffffff8114ccb0, 0xffffffff800031c4, 0x1, 0xffffffff800347f8, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0}, fstate = {f = {
      0x0 <repeats 32 times>}, fcsr = 0x0}, ti = 0xffffffff8114ae80}
```

The ```ret``` instruction at the end of ```__switch_to``` resumes a thread at this address.  

The scheduled out thread state has ```$ra``` pointing to same address.

```
(gdb) p/x oldthread->arch.state
$138 = {ra = 0xffffffff8000b3f8, sp = 0xffffffc002e04000, s = {0x0 <repeats 12 times>}, fstate = {f = {0x0 <repeats 32 times>}, fcsr = 0x0}, ti = 0xffffffff8116a9d8}
```

The ```riscv_switch_to``` disassembled listing is

```
(gdb) disassem 0xffffffff80002f3c
Dump of assembler code for function riscv_switch_to:
   0xffffffff80002f04 <+0>:	addi	sp,sp,-48
   0xffffffff80002f08 <+4>:	sd	ra,40(sp)
   0xffffffff80002f0c <+8>:	sd	s0,32(sp)
   0xffffffff80002f10 <+12>:	sd	s1,24(sp)
   0xffffffff80002f14 <+16>:	addi	s0,sp,48
   0xffffffff80002f18 <+20>:	mv	s1,ra
   0xffffffff80002f1c <+24>:	sd	a0,-40(s0)
   0xffffffff80002f20 <+28>:	sd	a1,-48(s0)
   0xffffffff80002f24 <+32>:	ld	a1,-48(s0)
   0xffffffff80002f28 <+36>:	ld	a0,-40(s0)
   0xffffffff80002f2c <+40>:	jal	ra,0xffffffff80002ee0 <__switch_to_aux>
   0xffffffff80002f30 <+44>:	ld	a1,-48(s0)
   0xffffffff80002f34 <+48>:	ld	a0,-40(s0)
   0xffffffff80002f38 <+52>:	jal	ra,0xffffffff800003a8 <__switch_to>
   0xffffffff80002f3c <+56>:	jal	ra,0xffffffff80002e70 <get_current>
   0xffffffff80002f40 <+60>:	mv	a5,a0
   0xffffffff80002f44 <+64>:	seqz	a5,a5
   0xffffffff80002f48 <+68>:	andi	a5,a5,255
   0xffffffff80002f4c <+72>:	beqz	a5,0xffffffff80002f78 <riscv_switch_to+116>
   0xffffffff80002f50 <+76>:	mv	a0,s1
   0xffffffff80002f54 <+80>:	mv	a1,s0
   0xffffffff80002f58 <+84>:	lui	a5,0x800f2
   0xffffffff80002f5c <+88>:	addi	a5,a5,1880 # 0xffffffff800f2758
   0xffffffff80002f60 <+92>:	li	a4,34
   0xffffffff80002f64 <+96>:	lui	a3,0x800f2
   0xffffffff80002f68 <+100>:	addi	a3,a3,1896 # 0xffffffff800f2768
   0xffffffff80002f6c <+104>:	lui	a2,0x800f2
   0xffffffff80002f70 <+108>:	addi	a2,a2,1840 # 0xffffffff800f2730
   0xffffffff80002f74 <+112>:	jal	ra,0xffffffff8006b9f0 <_panic>
   0xffffffff80002f78 <+116>:	nop
   0xffffffff80002f7c <+120>:	ld	ra,40(sp)
   0xffffffff80002f80 <+124>:	ld	s0,32(sp)
   0xffffffff80002f84 <+128>:	ld	s1,24(sp)
   0xffffffff80002f88 <+132>:	addi	sp,sp,48
   0xffffffff80002f8c <+136>:	ret
```

As you can see ```0xffffffff80002f3c``` is an address of a call to ```get_current``` which is a parameter to the ```assert``` check after a call to ```__switch_to```.
