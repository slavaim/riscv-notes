
Load an image with bbl+vmlinux+initramfs in QEMU with ```-S -s``` command options to wait for GDB.

```
$ /work/risc-v/riscv-qemu/build/bin/qemu-system-riscv64 -m 4G -kernel /work/risc-v/riscv-tools/riscv-pk/build-with-payload/bbl -nographic -S -s
```

Start GDB and connect to localhost:1234

```
$ /work/risc-v/riscv-gnu-toolchain/build/bin/riscv64-unknown-linux-gnu-gdb
GNU gdb (GDB) 7.12.50.20170124-git
Copyright (C) 2017 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "--host=x86_64-pc-linux-gnu --target=riscv64-unknown-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word".
(gdb) target remote localhost:1234
Remote debugging using localhost:1234
warning: No executable has been specified and target does not support
determining executable automatically.  Try using the "file" command.
0x0000000000001000 in ?? ()
(gdb) c
Continuing.
(gdb) ^C
Program received signal SIGINT, Interrupt.
0xffffffff800d9fa8 in ?? ()
(gdb) file /work/risc-v/linux/linux-4.6.2/vmlinux
A program is being debugged already.
Are you sure you want to change the file? (y or n) y
Reading symbols from /work/risc-v/linux/linux-4.6.2/vmlinux...done.
(gdb) bt
#0  0xffffffff800d9fa8 in current_thread_info () at ./arch/riscv/include/asm/irqflags.h:16
#1  current_clr_polling_and_test () at include/linux/sched.h:3075
#2  default_idle_call () at kernel/sched/idle.c:89
#3  0xffffffff800da088 in cpuidle_idle_call () at kernel/sched/idle.c:268
#4  cpu_idle_loop () at kernel/sched/idle.c:242
#5  cpu_startup_entry (state=<optimized out>) at kernel/sched/idle.c:291
#6  0xffffffff802ce9d0 in rest_init () at init/main.c:408
#7  0xffffffff80000a6c in start_kernel () at init/main.c:591
#8  0xffffffff80000040 in _start () at arch/riscv/kernel/head.S:36
Backtrace stopped: frame did not save the PC

```
