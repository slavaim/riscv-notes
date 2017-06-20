
```
(gdb) bt
#0  reboot () at kernel/platform/riscv-rv64/platform.c:73
#1  0xffffffff80001d7c in platform_halt (suggested_action=HALT_ACTION_HALT, reason=HALT_REASON_SW_PANIC) at kernel/platform/riscv-rv64/platform.c:104
#2  0xffffffff8006ba60 in _panic (caller=0xffffffff8000026c <handle_exception+328>, frame=0xffffffc002e039e8, fmt=0xffffffff800f30c0 "%s: page fault in non-paged area with interrupts disabled")
    at kernel/lib/debug/debug.c:38
#3  0xffffffff800068dc in do_page_fault (regs=0xffffffc002e039e8) at kernel/arch/riscv/faults.cpp:136
#4  0xffffffff8000026c in handle_exception () at kernel/arch/riscv/rv64/exception.S:212
Backtrace stopped: frame did not save the PC
(gdb) f 3
#3  0xffffffff800068dc in do_page_fault (regs=0xffffffc002e039e8) at kernel/arch/riscv/faults.cpp:136
136	    if (0x0 == (regs->sstatus & SR_PIE) && !user_mode(regs)) {
(gdb) p/x *regs
$129 = {sepc = 0xffffffff80006250, ra = 0xffffffff80067c44, sp = 0xffffffc002e03b10, gp = 0x0, tp = 0xffffffff8116a9d8, t0 = 0x6000, t1 = 0xffffffff800f315e, t2 = 0xffffffff800f31ae, 
  s0 = 0xffffffc002e03b40, s1 = 0xffffffff81169510, a0 = 0xffffffff81169510, a1 = 0x0, a2 = 0x10000, a3 = 0x51e8, a4 = 0xffffffff81169510, a5 = 0x0, a6 = 0x1cc2f85f14, a7 = 0x7, s2 = 0x120, 
  s3 = 0xffffffff8004f08c, s4 = 0xfffffffffffffff3, s5 = 0x8000000000000005, s6 = 0xffffffff8116a9d8, s7 = 0x0, s8 = 0x0, s9 = 0x0, s10 = 0x0, s11 = 0x0, t3 = 0x0, t4 = 0x0, t5 = 0x0, t6 = 0x0, 
  sstatus = 0x100, sbadaddr = 0x10, scause = 0x5}
(gdb) x/i 0xffffffff80006250
   0xffffffff80006250 <arch_mmu_context_switch(arch_aspace*, arch_aspace*)+52>:	ld	a5,16(a5)
(gdb) set $pc=0xffffffff80006250
(gdb) set $ra=0xffffffff80067c44
(gdb) set $sp=0xffffffc002e03b10
(gdb) set $s0=0xffffffc002e03b40
(gdb) bt
#0  arch_mmu_context_switch (old_aspace=0xffffffff81169510, aspace=0x0) at kernel/arch/riscv/mmu.cpp:714
#1  0xffffffff80067c44 in vmm_context_switch (oldspace=0xffffffff811693c0, newaspace=0x0) at kernel/kernel/vm/vmm.cpp:35
#2  0xffffffff80067c80 in vmm_context_switch (oldspace=0xffffffff811693c0, newaspace=0x0) at kernel/kernel/vm/vmm.cpp:40
#3  0xffffffff80002f3c in riscv_switch_to (prev=0xc8116a7b8, next=0xffffffff8116a7b8) at kernel/arch/riscv/include/arch/riscv/switch_to.h:33
#4  0xffffffff8000a63c in sched_preempt () at kernel/kernel/sched.c:314
#5  0xffffffff8000cf98 in thread_preempt () at kernel/kernel/thread.c:876
#6  0xffffffff80003990 in do_IRQ (cause=5, regs=0xffffffc002e03c90) at kernel/arch/riscv/irq.c:97
#7  0xffffffff8000026c in handle_exception () at kernel/arch/riscv/rv64/exception.S:212
Backtrace stopped: frame did not save the PC
(gdb) p/x (pt_regs*)0xffffffc002e03c90
$149 = 0xffffffc002e03c90
(gdb) p/x *(pt_regs*)0xffffffc002e03c90
$150 = {sepc = 0xffffffff8004f08c, ra = 0xffffffff8004e888, sp = 0xffffffc002e03db8, gp = 0x0, tp = 0xffffffff8116a9d8, t0 = 0xffffffff800066a4, t1 = 0xffffffff800f315e, t2 = 0xffffffff800f31ae, 
  s0 = 0xffffffc002e03e08, s1 = 0xffffffff80067d24, a0 = 0xffffffff8015a2c0, a1 = 0xfffffffffffffff3, a2 = 0x12, a3 = 0x51b0, a4 = 0xfffffffffffffff3, a5 = 0xffffffff8015a2c0, a6 = 0x1cc2f85f14, 
  a7 = 0x1cc2f85f10, s2 = 0xfffffffffffffff3, s3 = 0x292f047394, s4 = 0xfffffffffffffff3, s5 = 0x5, s6 = 0x0, s7 = 0x0, s8 = 0x0, s9 = 0x0, s10 = 0x0, s11 = 0x0, t3 = 0x0, t4 = 0x0, t5 = 0x0, t6 = 0x0, 
  sstatus = 0x120, sbadaddr = 0xfffffffffffffff3, scause = 0x8000000000000005}
(gdb) x/i 0xffffffff8004f08c
   0xffffffff8004f08c <mxtl::Canary<1447903571ul>::Assert() const>:	addi	sp,sp,-32
(gdb) set $pc=0xffffffff8004f08c
(gdb) set $ra=0xffffffff8004e888
(gdb) set $sp=0xffffffc002e03db8
(gdb) set $s0=0xffffffc002e03e08
(gdb) bt
#0  mxtl::Canary<1447903571ul>::Assert (this=0x8000000000000005) at system/ulib/mxtl/include/mxtl/canary.h:63
#1  0xffffffff8004e888 in VmAspace::PageFault (this=0xffffffff8015a2a8 <VmAspace::KernelAspaceInitPreHeap()::_kernel_aspace>, va=18446744073709551603, flags=18) at kernel/kernel/vm/vm_aspace.cpp:522
#2  0xffffffff80067d24 in vmm_page_fault_handler (addr=18446744073709551603, flags=18) at kernel/kernel/vm/vmm.cpp:63
#3  0xffffffff800069b8 in do_page_fault (regs=0xffffffc002e03ed8) at kernel/arch/riscv/faults.cpp:171
#4  0xffffffff8000026c in handle_exception () at kernel/arch/riscv/rv64/exception.S:212
```
