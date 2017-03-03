
Sometimes a returned address is not a next instruction after a call as you might have used to on x86 for example. This happens when the link register ```ra``` is set to something diffirent before such instructions like ```jr``` . The example of this is a call to fault handlers from ```handle_exception```.

```
#0  do_page_fault (regs=0xfffffffff8e4dee8) at arch/riscv/mm/fault.c:17
#1  0xffffffff800b7cd8 in handle_exception () at arch/riscv/kernel/entry.S:187
```

The dissambler listing for 0xffffffff800b7cd8 is

```
   0xffffffff800b7cd8 <+372>:	ld	s0,256(sp)
   0xffffffff800b7cdc <+376>:	csrci	sstatus,2
   0xffffffff800b7ce0 <+380>:	andi	s0,s0,256
```

Which is consistent with

```
ret_from_syscall:
	/* Set user a0 to kernel a0 */
	REG_S a0, PT_A0(sp)
	/* Trace syscalls, but only if requested by the user. */
	REG_L t0, TASK_THREAD_INFO(tp)
	REG_L t0, TI_FLAGS(t0)
	andi t0, t0, _TIF_SYSCALL_TRACE
	bnez t0, handle_syscall_trace_exit
```

The link register is set at the start of ```handle_exception``` before calling any handler.

```
ENTRY(handle_exception)
	SAVE_ALL

	/* Set sscratch register to 0, so that if a recursive exception
	   occurs, the exception vector knows it came from the kernel */
	csrw sscratch, x0

	/* Compute address of current thread_info */
	li tp, ~(THREAD_SIZE-1)
	and tp, tp, sp
	/* Set current pointer */
	REG_L tp, TI_TASK(tp)

1:	auipc gp, %pcrel_hi(_gp)
	addi gp, gp, %pcrel_lo(1b)

	la ra, ret_from_exception
```

So when you backtrace a call stack the link register points to ```ret_from_exception``` instead of the instruction next to ```jr``` which is a jump to ```do_trap_unknown``` executing which doesn't make sense after returning from a handler.

```
	/* Handle other exceptions */
	slli t0, s4, LGPTR
	la t1, excp_vect_table
	la t2, excp_vect_table_end
	move a0, sp /* pt_regs */
	add t0, t1, t0
	/* Check if exception code lies within bounds */
	bgeu t0, t2, 1f
	REG_L t0, 0(t0)
	jr t0
1:
	tail do_trap_unknown
```

This might make it difficult to locate an address from where a function has been called.

