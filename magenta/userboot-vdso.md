
```VDSO``` mapped after the ```libuserboot.so``` ( system/core/userboot/libuserboot.so ) which is included in the kernel through /work/fuchsia/magenta/kernel/lib/userboot/userboot-image.S

```
[00005.216] 00000.00000> userboot: userboot rodata       0 @ [0x292f043000,0x292f045000)
[00005.218] 00000.00000> userboot: userboot code    0x2000 @ [0x292f045000,0x292f05a000)
[00005.219] 00000.00000> userboot: vdso/full rodata       0 @ [0x292f05a000,0x292f05f000)
[00005.220] 00000.00000> userboot: vdso/full code    0x5000 @ [0x292f05f000,0x292f061000)
[00005.247] 00000.00000> userboot: entry point             @ 0x292f046da0
```

libuserboot.so accesses VDSO functions through the generated ```build-magenta-qemu-riscv-rv64/system/core/userboot/vdso-syms.ld```

```
PROVIDE_HIDDEN(_mx_acpi_cache_flush = CODE_END + 0x0000000000006a80);
PROVIDE_HIDDEN(mx_acpi_cache_flush = CODE_END + 0x0000000000006a80);
PROVIDE_HIDDEN(_mx_acpi_uefi_rsdp = CODE_END + 0x0000000000006a58);
PROVIDE_HIDDEN(mx_acpi_uefi_rsdp = CODE_END + 0x0000000000006a58);
PROVIDE_HIDDEN(_mx_bootloader_fb_get_info = CODE_END + 0x00000000000067b0);
PROVIDE_HIDDEN(mx_bootloader_fb_get_info = CODE_END + 0x00000000000067b0);
PROVIDE_HIDDEN(_mx_cache_flush = CODE_END + 0x0000000000005000);
PROVIDE_HIDDEN(mx_cache_flush = CODE_END + 0x0000000000005000);
PROVIDE_HIDDEN(_mx_channel_call = CODE_END + 0x000000000000505c);
PROVIDE_HIDDEN(mx_channel_call = CODE_END + 0x000000000000505c);
PROVIDE_HIDDEN(_mx_channel_create = CODE_END + 0x0000000000005c70);
PROVIDE_HIDDEN(mx_channel_create = CODE_END + 0x0000000000005c70);
PROVIDE_HIDDEN(_mx_channel_read = CODE_END + 0x0000000000005c98);
PROVIDE_HIDDEN(mx_channel_read = CODE_END + 0x0000000000005c98);
PROVIDE_HIDDEN(_mx_channel_write = CODE_END + 0x0000000000005cc0);
PROVIDE_HIDDEN(mx_channel_write = CODE_END + 0x0000000000005cc0);
PROVIDE_HIDDEN(_mx_clock_adjust = CODE_END + 0x0000000000006828);
PROVIDE_HIDDEN(mx_clock_adjust = CODE_END + 0x0000000000006828);
PROVIDE_HIDDEN(_mx_cprng_add_entropy = CODE_END + 0x0000000000006440);
PROVIDE_HIDDEN(mx_cprng_add_entropy = CODE_END + 0x0000000000006440);
PROVIDE_HIDDEN(_mx_cprng_draw = CODE_END + 0x0000000000006418);
PROVIDE_HIDDEN(mx_cprng_draw = CODE_END + 0x0000000000006418);
PROVIDE_HIDDEN(_mx_deadline_after = CODE_END + 0x000000000000512c);
.....
```
