
The following files contain architecture specific macros to support syscals.


```
system/ulib/magenta/syscalls-riscv64.S
system/ulib/magenta/magenta-syscall-riscv64.S
```

The ```system/ulib/magenta/magenta-syscall-riscv64.S``` file contains ```magenta_syscall``` which calls ```scall``` instruction to enter supervisor mode. The code also adds a fingeprint to a syscall number

```
    addi sp, sp, -8
    sd   t0, 0(sp)
    li   t0, (0x00ff00ff00000000 | \num)
    scall
    // This symbol at the return address identifies this as an approved call site.
    .hidden CODE_SYSRET_\name\()_VIA_\caller
    CODE_SYSRET_\name\()_VIA_\caller\():
    ld   t0, 0(sp)
    addi sp, sp, 8
```

The fingeprint generates pretty ugly volumes code and I believe should be removed in the future, adding a fingeprint requires 3 instructions ( ```scall``` and ```ecall``` is the same instruction it depends on the RISC-V ISA revision used by the gdb and gcc )
```
   0x292f05fc98:	addi	sp,sp,-8
   0x292f05fc9c:	sd	t0,0(sp)
   0x292f05fca0:	lui	t0,0xff0
   0x292f05fca4:	addiw	t0,t0,255
   0x292f05fca8:	slli	t0,t0,0x20
   0x292f05fcac:	addi	t0,t0,17
   0x292f05fcb0:	ecall
   0x292f05fcb4:	ld	t0,0(sp)
   0x292f05fcb8:	addi	sp,sp,8
   0x292f05fcbc:	ret
```

The fingepring is checked by ```riscv_syscall``` .

The ```copy_to_user``` function is used to copy data to user mode

```
#3  0xffffffff80003510 in arch_copy_to_user (dst=0x1cc2f85fe8, src=0xffffffc002e03d8c, len=4) at kernel/arch/riscv/user_copy.c:44
#4  0xffffffff8001cb10 in copy_to_user_unsafe (dst=0x1cc2f85fe8, src=0xffffffc002e03d8c, len=4) at kernel/lib/user_copy/include/lib/user_copy.h:16
#5  0xffffffff8001e5e0 in user_ptr<int>::copy_to_user<int> (this=0xffffffc002e03d58, src=@0xffffffc002e03d8c: 786048147) at kernel/lib/user_copy/include/lib/user_copy/user_ptr.h:49
#6  0xffffffff8002608c in sys_log_create (options=512, out=...) at kernel/lib/syscalls/syscalls_magenta.cpp:188
#7  0xffffffff8001ac5c in invoke_syscall (syscall_num=70, pc=176882582780, arg1=512, arg2=123530141672, arg3=0, arg4=0, arg5=0, arg6=123530141672, arg7=0, arg8=0)
    at ./build-magenta-qemu-riscv-rv64/gen/include/magenta/syscall-invocation-cases.inc:538
#8  0xffffffff8001c3a4 in riscv_syscall (regs=0xffffffc002e03ed8) at kernel/lib/syscalls/syscalls.cpp:187
#9  0xffffffff8000026c in handle_exception () at kernel/arch/riscv/rv64/exception.S:212
```
