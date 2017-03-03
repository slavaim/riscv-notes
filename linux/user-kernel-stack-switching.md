
The task stack is switched when a task enters in kernel mode in ```handle_exception ( arch/riscv/kernel/entry.S )``` which processes all type of exceptions including system calls and interrupts.

The scratch register ```sscratch``` contains the kernel mode stack pointer if the task enters the kernel mode from a user mode.

```
	/* If coming from userspace, preserve the user stack pointer and load
	   the kernel stack pointer.  If we came from the kernel, sscratch
	   will contain 0, and we should continue on the current stack. */
	csrrw sp, sscratch, sp
	bnez sp, _save_context

_restore_kernel_sp:
	csrr sp, sscratch
_save_context:
	addi sp, sp, -(PT_SIZE)
	REG_S x1,  PT_RA(sp)
	REG_S x3,  PT_GP(sp)
  ...
```

Before the interrups are enabled the scratch register is zeroed so subsequent calls to ```handle_exception``` will find that the current stack pointer register ```sp``` contains kernel mode stack address.

```
/* Set sscratch register to 0, so that if a recursive exception
	   occurs, the exception vector knows it came from the kernel */
	csrw sscratch, x0
  
  ...
  
  handle_syscall:
  ...
	/* System calls run with interrupts enabled */
	csrs sstatus, SR_IE
  
  ..
```

The scratch register is used to save the current task's kernel mode stack pointer when control returns back to a user mode. The system removes the task from a CPU ( i.e. reschedules ) when executing kernel mode code. If a task is being executed in user mode the scheduling is done either when processing a return from an interrupt or inside a system call made by the task. So when the task is put back on a CPU( i.e. scheduled ) this is done from the kernel mode so ```resume_userspace``` will be called again and the scratch register will be set back to task's kernel mode stack value so on next system call or interrupt the correct kernel mode stack pointer will be fetched. 

```
resume_userspace:
	/* Interrupts must be disabled here so flags are checked atomically */
	REG_L s0, TASK_THREAD_INFO(tp)
	REG_L s0, TI_FLAGS(s0) /* current_thread_info->flags */
	andi s1, s0, _TIF_WORK_MASK
	bnez s1, work_pending

	/* Save unwound kernel stack pointer in sscratch */
	addi s0, sp, PT_SIZE
	csrw sscratch, s0
restore_all:
	RESTORE_ALL
	sret
```
