
```
(gdb) bt
#0  mxtl::WAVLTree<unsigned long, mxtl::RefPtr<VmAddressRegionOrMapping>, mxtl::DefaultKeyedObjectTraits<unsigned long, VmAddressRegionOrMapping>, VmAddressRegionOrMapping::WAVLTreeTraits, mxtl::tests::intrusive_containers::DefaultWAVLTreeObserver>::iterator_impl<mxtl::WAVLTree<unsigned long, mxtl::RefPtr<VmAddressRegionOrMapping>, mxtl::DefaultKeyedObjectTraits<unsigned long, VmAddressRegionOrMapping>, VmAddressRegionOrMapping::WAVLTreeTraits, mxtl::tests::intrusive_containers::DefaultWAVLTreeObserver>::iterator_traits>::operator-- (
    this=0xffffffff8015a541 <VmAspace::KernelAspaceInitPreHeap()::_kernel_root_vmar+105>) at system/ulib/mxtl/include/mxtl/intrusive_wavl_tree.h:548
#1  0xffffffff80041a44 in VmAddressRegion::FindRegionLocked (this=0xffffffff8015a4d8 <VmAspace::KernelAspaceInitPreHeap()::_kernel_root_vmar>, addr=18446744073709551603)
    at kernel/kernel/vm/vm_address_region.cpp:355
#2  0xffffffff80041d64 in VmAddressRegion::PageFault (this=0xffffffff8015a4d8 <VmAspace::KernelAspaceInitPreHeap()::_kernel_root_vmar>, va=18446744073709551603, pf_flags=18)
    at kernel/kernel/vm/vm_address_region.cpp:383
#3  0xffffffff8004e8f4 in VmAspace::PageFault (this=0xffffffff8015a2a8 <VmAspace::KernelAspaceInitPreHeap()::_kernel_aspace>, va=18446744073709551603, flags=18) at kernel/kernel/vm/vm_aspace.cpp:531
#4  0xffffffff80067d14 in vmm_page_fault_handler (addr=18446744073709551603, flags=18) at kernel/kernel/vm/vmm.cpp:63
#5  0xffffffff800069ec in do_page_fault (regs=0xffffffc002e03ed8) at kernel/arch/riscv/faults.cpp:171
#6  0xffffffff8000026c in handle_exception () at kernel/arch/riscv/rv64/exception.S:212
```

```
(gdb) bt
#0  VmAddressRegionOrMapping::GetKey (this=0xffffffff811457f0) at kernel/include/kernel/vm/vm_address_region.h:120
#1  0xffffffff8004a238 in mxtl::DefaultKeyedObjectTraits<unsigned long, VmAddressRegionOrMapping>::GetKey (obj=...) at system/ulib/mxtl/include/mxtl/intrusive_container_utils.h:47
#2  0xffffffff80049d90 in mxtl::WAVLTree<unsigned long, mxtl::RefPtr<VmAddressRegionOrMapping>, mxtl::DefaultKeyedObjectTraits<unsigned long, VmAddressRegionOrMapping>, VmAddressRegionOrMapping::WAVLTreeTraits, mxtl::tests::intrusive_containers::DefaultWAVLTreeObserver>::internal_upper_lower_bound<mxtl::WAVLTree<unsigned long, mxtl::RefPtr<VmAddressRegionOrMapping>, mxtl::DefaultKeyedObjectTraits<unsigned long, VmAddressRegionOrMapping>, VmAddressRegionOrMapping::WAVLTreeTraits, mxtl::tests::intrusive_containers::DefaultWAVLTreeObserver>::UpperBoundTraits> (
    this=0xffffffff8015a540 <VmAspace::KernelAspaceInitPreHeap()::_kernel_root_vmar+104>, key=@0xffffffc002e03d10: 18446744073709551603) at system/ulib/mxtl/include/mxtl/intrusive_wavl_tree.h:1601
#3  0xffffffff80046fb4 in mxtl::WAVLTree<unsigned long, mxtl::RefPtr<VmAddressRegionOrMapping>, mxtl::DefaultKeyedObjectTraits<unsigned long, VmAddressRegionOrMapping>, VmAddressRegionOrMapping::WAVLTreeTraits, mxtl::tests::intrusive_containers::DefaultWAVLTreeObserver>::upper_bound (this=0xffffffff8015a540 <VmAspace::KernelAspaceInitPreHeap()::_kernel_root_vmar+104>, 
    key=@0xffffffc002e03d10: 18446744073709551603) at system/ulib/mxtl/include/mxtl/intrusive_wavl_tree.h:288
#4  0xffffffff800452bc in mxtl::WAVLTree<unsigned long, mxtl::RefPtr<VmAddressRegionOrMapping>, mxtl::DefaultKeyedObjectTraits<unsigned long, VmAddressRegionOrMapping>, VmAddressRegionOrMapping::WAVLTreeTraits, mxtl::tests::intrusive_containers::DefaultWAVLTreeObserver>::upper_bound (this=0xffffffff8015a540 <VmAspace::KernelAspaceInitPreHeap()::_kernel_root_vmar+104>, 
    key=@0xffffffc002e03d10: 18446744073709551603) at system/ulib/mxtl/include/mxtl/intrusive_wavl_tree.h:292
#5  0xffffffff80041a38 in VmAddressRegion::FindRegionLocked (this=0xffffffff8015a4d8 <VmAspace::KernelAspaceInitPreHeap()::_kernel_root_vmar>, addr=18446744073709551603)
    at kernel/kernel/vm/vm_address_region.cpp:355
#6  0xffffffff80041d64 in VmAddressRegion::PageFault (this=0xffffffff8015a4d8 <VmAspace::KernelAspaceInitPreHeap()::_kernel_root_vmar>, va=18446744073709551603, pf_flags=18)
    at kernel/kernel/vm/vm_address_region.cpp:383
#7  0xffffffff8004e8f4 in VmAspace::PageFault (this=0xffffffff8015a2a8 <VmAspace::KernelAspaceInitPreHeap()::_kernel_aspace>, va=18446744073709551603, flags=18) at kernel/kernel/vm/vm_aspace.cpp:531
#8  0xffffffff80067d14 in vmm_page_fault_handler (addr=18446744073709551603, flags=18) at kernel/kernel/vm/vmm.cpp:63
#9  0xffffffff800069ec in do_page_fault (regs=0xffffffc002e03ed8) at kernel/arch/riscv/faults.cpp:171
#10 0xffffffff8000026c in handle_exception () at kernel/arch/riscv/rv64/exception.S:212

```
