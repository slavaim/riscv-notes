
```
#0  0xffffffff80004ffc in riscv64_mmu_map (aspace=0x2e01540, vaddr_in=24, paddr_in=18446744071562422724, size_in=0, page_prot=..., top_table=0x1)
#1  0xffffffff800057ec in arch_mmu_map (aspace=0xffffffff80154638 <VmAspace::KernelAspaceInitPreHeap()::_kernel_aspace+336>, vaddr=18446743798879883264, paddr=2157080576, count=1, mmu_flags=24, 
    mapped=0xffffffff81146a00) at kernel/arch/riscv/mmu.cpp:300
#2  0xffffffff80051868 in VmMapping::PageFault (this=0xffffffff811686f8, va=18446743798879883264, pf_flags=17) at kernel/kernel/vm/vm_mapping.cpp:620
#3  0xffffffff80040eec in VmAddressRegion::PageFault (this=0xffffffff80154718 <VmAspace::KernelAspaceInitPreHeap()::_kernel_root_vmar>, va=18446743798879884608, pf_flags=17)
    at kernel/kernel/vm/vm_address_region.cpp:386
#4  0xffffffff8004d860 in VmAspace::PageFault (this=0xffffffff801544e8 <VmAspace::KernelAspaceInitPreHeap()::_kernel_aspace>, va=18446743798879884608, flags=17) at kernel/kernel/vm/vm_aspace.cpp:531
#5  0xffffffff800661a4 in vmm_page_fault_handler (addr=18446743798879884608, flags=17) at kernel/kernel/vm/vmm.cpp:58
#6  0xffffffff80003884 in do_page_fault (regs=0xffffffff81146c18) at kernel/arch/riscv/faults.c:133
#7  0xffffffff8000026c in handle_exception () at kernel/arch/riscv/rv64/exception.S:212
```
