
19/02/17 
```
make ARCH=riscv CROSS_COMPILE=/work/risc-v/riscv-tools/build/bin/riscv64-unknown-elf-  defconfig
make ARCH=riscv CROSS_COMPILE=/work/risc-v/riscv-tools/build/bin/riscv64-unknown-elf-  menuconfig
make -j4 ARCH=riscv CROSS_COMPILE=/work/risc-v/riscv-tools/build/bin/riscv64-unknown-elf- vmlinux
```
