VDSO image in the kernel.

VDSO elf image is embedded in the kernel. The image is added to the kernel by ```vdso/include/lib/rodso-asm.h``` when the macro

```
#define RODSO_IMAGE(name, NAME) \
    rodso_image_section name, NAME##_CODE_END; \
    DATA(name##_image) \
    .incbin NAME##_FILENAME, 0, NAME##_CODE_END; \
    END(name##_image)
```

expands to

```
#define RODSO_IMAGE(vdso, VDSO) \
    rodso_image_section vdso, VDSO_CODE_END; \
    DATA(vdso_image) \
    .incbin VDSO_FILENAME, 0, VDSO_CODE_END; \
    END(vdso_image)
```

the definitions for VDSO_CODE_END and VDSO_FILENAME are generated during the build into 
 build-magenta-qemu-riscv-rv64/kernel/lib/vdso/vdso-code.h the typical values are

```
#define VDSO_FILENAME "./build-magenta-qemu-riscv-rv64/system/ulib/magenta/libmagenta.so"
#define VDSO_CODE_END 0x0000000000007000
```

The image can be located in the kernel by vdso_image variable

```
(gdb) p/x &vdso_image[0]
$112 = 0xffffffff8012a000
(gdb) x/c 0xffffffff8012a000
0xffffffff8012a000 <vdso_image>:	127 '\177'
(gdb) x/10c 0xffffffff8012a000
0xffffffff8012a000 <vdso_image>:	127 '\177'	69 'E'	76 'L'	70 'F'	2 '\002'	1 '\001'	1 '\001'	0 '\000'
0xffffffff8012a008 <vdso_image+8>:	0 '\000'	0 '\000'
```
