

19/02/17 Starting GDB

```
$ export PATH=$PATH:/work/risc-v/riscv-tools/build/bin
$ riscv64-unknown-elf-gdb
```

19/02/17 In the debugger
```
(gdb) set architecture riscv:rv64
(gdb) file /work/risc-v/linux/linux-4.6.2/vmlinux
```
