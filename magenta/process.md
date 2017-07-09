
Process address space unmapping on exit.
```
#0  free_pt (va=0xffffffff8012d000 <mmu_initial_mappings>) at kernel/arch/riscv/mmu.cpp:56
#1  0xffffffff80005b98 in riscv64_mmu_pte_gc (vaddr_in=123529879552, size_in=262144, top_table=0xffffffff83257000) at kernel/arch/riscv/mmu.cpp:404
#2  0xffffffff8000604c in riscv64_mmu_pt_gc (vaddr_in=123529879552, size_in=262144, top_table=0xffffffff83257000) at kernel/arch/riscv/mmu.cpp:617
#3  0xffffffff80006398 in riscv64_mmu_unmap (vaddr_in=123529879552, size_in=262144, top_table=0xffffffff83257000) at kernel/arch/riscv/mmu.cpp:700
#4  0xffffffff800065d8 in arch_mmu_unmap (aspace=0xffffffff81164490, vaddr=123529879552, count=64, unmapped=0x0) at kernel/arch/riscv/mmu.cpp:742
#5  0xffffffff80052dcc in VmMapping::UnmapLocked (this=0xffffffff81165440, base=123529879552, size=262144) at kernel/kernel/vm/vm_mapping.cpp:258
#6  0xffffffff80053ab4 in VmMapping::DestroyLocked (this=0xffffffff81165440) at kernel/kernel/vm/vm_mapping.cpp:477
#7  0xffffffff80043064 in VmAddressRegion::DestroyLocked (this=0xffffffff811644f0) at kernel/kernel/vm/vm_address_region.cpp:319
#8  0xffffffff8004f19c in VmAspace::Destroy (this=0xffffffff81164340) at kernel/kernel/vm/vm_aspace.cpp:242
#9  0xffffffff800999bc in ProcessDispatcher::SetStateLocked (this=0xffffffff81164030, s=ProcessDispatcher::State::DEAD) at kernel/lib/magenta/process_dispatcher.cpp:339
#10 0xffffffff80099680 in ProcessDispatcher::RemoveThread (this=0xffffffff81164030, t=0xffffffff811654e0) at kernel/lib/magenta/process_dispatcher.cpp:270
#11 0xffffffff800a42c0 in UserThread::Exiting (this=0xffffffff811654e0) at kernel/lib/magenta/user_thread.cpp:432
#12 0xffffffff800a45bc in UserThread::ThreadUserCallback (new_state=THREAD_USER_STATE_EXIT, arg=0xffffffff811654e0) at kernel/lib/magenta/user_thread.cpp:525
#13 0xffffffff8000d0ac in thread_exit (retcode=0) at kernel/kernel/thread.c:546
#14 0xffffffff800a3de0 in UserThread::Exit (this=0xffffffff811654e0) at kernel/lib/magenta/user_thread.cpp:299
#15 0xffffffff80099284 in ProcessDispatcher::Exit (this=0xffffffff81164030, retcode=-10) at kernel/lib/magenta/process_dispatcher.cpp:174
#16 0xffffffff80030f94 in sys_process_exit (retcode=-10) at kernel/lib/syscalls/syscalls_task.cpp:322
#17 0xffffffff8001a400 in invoke_syscall (syscall_num=29, pc=176882581140, arg1=18446744073709551606, arg2=37, arg3=123530140680, arg4=0, arg5=37, arg6=0, arg7=123530141100, arg8=123530141096)
    at ./build-magenta-qemu-riscv-rv64/gen/include/magenta/syscall-invocation-cases.inc:225
#18 0xffffffff8001cd68 in riscv_syscall (regs=0xffffffc002e03ed8) at kernel/lib/syscalls/syscalls.cpp:187
#19 0xffffffff80000148 in handle_exception () at kernel/arch/riscv/rv64/exception.S:212
```
