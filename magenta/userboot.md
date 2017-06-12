Starting the init process.

The init process adress space initialization.

```
#2  0xffffffff80004690 in arch_mmu_init_aspace (aspace=0xffffffff81168310, base=16777216, size=274861125632, flags=0) at kernel/arch/riscv/mmu.cpp:73
#3  0xffffffff8004bb64 in VmAspace::Init (this=0xffffffff811681c0) at kernel/kernel/vm/vm_aspace.cpp:142
#4  0xffffffff8004bdb0 in VmAspace::Create (flags=0, name=0x0) at kernel/kernel/vm/vm_aspace.cpp:180
#5  0xffffffff8008f7e0 in ProcessDispatcher::Initialize (this=0xffffffff81167eb0) at kernel/lib/magenta/process_dispatcher.cpp:147
#6  0xffffffff8008ef30 in ProcessDispatcher::Create (job=..., name=..., flags=0, dispatcher=0xffffffff81146e48, rights=0xffffffff81146e3c, root_vmar_disp=0xffffffff81146e40, 
    root_vmar_rights=0xffffffff81146e38) at kernel/lib/magenta/process_dispatcher.cpp:73
#7  0xffffffff80032b84 in attempt_userboot () at kernel/lib/userboot/userboot.cpp:283
#8  0xffffffff800330ec in userboot_init (level=720895) at kernel/lib/userboot/userboot.cpp:357
#9  0xffffffff80005da8 in lk_init_level (required_flag=LK_INIT_FLAG_PRIMARY_CPU, start_level=655360, stop_level=720895) at kernel/top/init.c:86
#10 0xffffffff80005e4c in lk_primary_cpu_init_level (start_level=655360, stop_level=720895) at kernel/include/lk/init.h:51
#11 0xffffffff800060fc in bootstrap2 (arg=0x0) at kernel/top/main.c:136
#12 0xffffffff8000a78c in initial_thread_func () at kernel/kernel/thread.c:84
#13 0xffffffff8000a74c in init_thread_struct (t=0xffffffff81144be0, name=0x0) at kernel/kernel/thread.c:72

```
