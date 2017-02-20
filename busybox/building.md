

```
$ export PATH=$PATH:/work/risc-v/riscv-gnu-toolchain/build/bin
$ curl -L http://busybox.net/downloads/busybox-1.21.1.tar.bz2 | tar -xj
$ cd busybox-1.21.1
$ make allnoconfig
```

changes for .config file

```
CONFIG_CROSS_COMPILER_PREFIX="riscv64-unknown-linux-gnu-"
CONFIG_SYSROOT="/work/risc-v/riscv-gnu-toolchain/build/sysroot"
CONFIG_STATIC=y
CONFIG_FEATURE_INSTALLER=y
CONFIG_INIT=y
CONFIG_ASH=y
CONFIG_ASH_JOB_CONTROL=n
CONFIG_MOUNT=y
```

then

```
$ make -j8
```
