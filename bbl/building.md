
Building BBL with a Linux kernel as a payload and debug symbols.

```
$ cd /work/risc-v/riscv-tools/riscv-pk
$ mkdir build-with-payload
$ export PATH=$PATH:/work/risc-v/riscv-tools/build/bin
$ export CFLAGS=-ggdb
$ ../configure --prefix=$RISCV/bin/riscv64-unknown-elf --host=riscv64-unknown-elf --with-payload=/work/risc-v/linux/linux-4.6.2/vmlinux
$ make
```
