
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
