This folder contains an unstripped system image ```bbl-vmlinux-initramfs``` and a Linux kernel ```vmlinux``` from the image to faciltate debugging.

To load the image in QEMU use the following commans

```
$ /work/risc-v/riscv-qemu/build/bin/qemu-system-riscv64 -m 4G -kernel /work/risc-v/riscv-tools/riscv-pk/build-with-payload/bbl -nographic
```

Below is a snapshot of console output when the system was running

```
              vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv
                  vvvvvvvvvvvvvvvvvvvvvvvvvvvv
rrrrrrrrrrrrr       vvvvvvvvvvvvvvvvvvvvvvvvvv
rrrrrrrrrrrrrrrr      vvvvvvvvvvvvvvvvvvvvvvvv
rrrrrrrrrrrrrrrrrr    vvvvvvvvvvvvvvvvvvvvvvvv
rrrrrrrrrrrrrrrrrr    vvvvvvvvvvvvvvvvvvvvvvvv
rrrrrrrrrrrrrrrrrr    vvvvvvvvvvvvvvvvvvvvvvvv
rrrrrrrrrrrrrrrr      vvvvvvvvvvvvvvvvvvvvvv  
rrrrrrrrrrrrr       vvvvvvvvvvvvvvvvvvvvvv    
rr                vvvvvvvvvvvvvvvvvvvvvv      
rr            vvvvvvvvvvvvvvvvvvvvvvvv      rr
rrrr      vvvvvvvvvvvvvvvvvvvvvvvvvv      rrrr
rrrrrr      vvvvvvvvvvvvvvvvvvvvvv      rrrrrr
rrrrrrrr      vvvvvvvvvvvvvvvvvv      rrrrrrrr
rrrrrrrrrr      vvvvvvvvvvvvvv      rrrrrrrrrr
rrrrrrrrrrrr      vvvvvvvvvv      rrrrrrrrrrrr
rrrrrrrrrrrrrr      vvvvvv      rrrrrrrrrrrrrr
rrrrrrrrrrrrrrrr      vv      rrrrrrrrrrrrrrrr
rrrrrrrrrrrrrrrrrr          rrrrrrrrrrrrrrrrrr
rrrrrrrrrrrrrrrrrrrr      rrrrrrrrrrrrrrrrrrrr
rrrrrrrrrrrrrrrrrrrrrr  rrrrrrrrrrrrrrrrrrrrrr

       INSTRUCTION SETS WANT TO BE FREE
[    0.000000] Linux version 4.6.2-00044-gdf91b31 (slava@slava-P34V2) (gcc version 6.1.0 (GCC) ) #18 Wed Feb 22 18:38:43 AEDT 2017
[    0.000000] Available physical memory: 1968MB
[    0.000000] Initial ramdisk at: 0xffffffff80014b80 (654451 bytes)
[    0.000000] Zone ranges:
[    0.000000]   Normal   [mem 0x0000000082800000-0x00000000fd7fffff]
[    0.000000] Movable zone start for each node
[    0.000000] Early memory node ranges
[    0.000000]   node   0: [mem 0x0000000082800000-0x00000000fd7fffff]
[    0.000000] Initmem setup node 0 [mem 0x0000000082800000-0x00000000fd7fffff]
[    0.000000] Built 1 zonelists in Zone order, mobility grouping on.  Total pages: 496920
[    0.000000] Kernel command line: 
[    0.000000] PID hash table entries: 4096 (order: 3, 32768 bytes)
[    0.000000] Dentry cache hash table entries: 262144 (order: 9, 2097152 bytes)
[    0.000000] Inode-cache hash table entries: 131072 (order: 8, 1048576 bytes)
[    0.000000] Sorting __ex_table...
[    0.000000] Memory: 1980816K/2015232K available (2167K kernel code, 106K rwdata, 444K rodata, 724K init, 220K bss, 34416K reserved, 0K cma-reserved)
[    0.000000] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=1, Nodes=1
[    0.000000] NR_IRQS:0 nr_irqs:0 0
[    0.000000] clocksource: riscv_clocksource: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 191126044627 ns
[    0.000000] Calibrating delay loop (skipped), value calculated using timer frequency.. 20.00 BogoMIPS (lpj=100000)
[    0.000000] pid_max: default: 32768 minimum: 301
[    0.000000] Mount-cache hash table entries: 4096 (order: 3, 32768 bytes)
[    0.000000] Mountpoint-cache hash table entries: 4096 (order: 3, 32768 bytes)
[    0.100000] devtmpfs: initialized
[    0.110000] clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 19112604462750000 ns
[    0.130000] NET: Registered protocol family 16
[    0.150000] vgaarb: loaded
[    0.160000] clocksource: Switched to clocksource riscv_clocksource
[    0.170000] NET: Registered protocol family 2
[    0.180000] TCP established hash table entries: 16384 (order: 5, 131072 bytes)
[    0.180000] TCP bind hash table entries: 16384 (order: 5, 131072 bytes)
[    0.180000] TCP: Hash tables configured (established 16384 bind 16384)
[    0.180000] UDP hash table entries: 1024 (order: 3, 32768 bytes)
[    0.190000] UDP-Lite hash table entries: 1024 (order: 3, 32768 bytes)
[    0.190000] NET: Registered protocol family 1
[    0.400000] Unpacking initramfs...
[    0.670000] console [sbi_console0] enabled
[    0.680000] futex hash table entries: 256 (order: 0, 6144 bytes)
[    0.680000] workingset: timestamp_bits=61 max_order=19 bucket_order=0
[    0.760000] io scheduler noop registered
[    0.760000] io scheduler cfq registered (default)
[    0.940000] Freeing unused kernel memory: 724K (ffffffff80000000 - ffffffff800b5000)
[    0.940000] This architecture does not have kernel memory protection.
Welcome to RISC-V Linux Live (/init)


BusyBox v1.21.1 (2017-02-21 19:21:40 AEDT) built-in shell (ash)
Enter 'help' for a list of built-in commands.

# help
Built-in commands:
------------------
	. : break cd chdir continue eval exec exit export false hash
	help local pwd read readonly return set shift times trap true
	type ulimit umask unset wait

# ls -la /
drwxrwxrwt    6 0        0              140 Jan  1 00:00 .
drwxrwxrwt    6 0        0              140 Jan  1 00:00 ..
drwxrwxr-x    2 1000     1000           940 Feb 22  2017 bin
drwxrwxr-x    2 1000     1000            60 Feb 22  2017 dev
-rwxrwxr-x    1 1000     1000           553 Feb 22  2017 init
drwxrwxr-x    2 1000     1000           300 Feb 22  2017 sbin
drwxrwxr-x    4 1000     1000            80 Feb 22  2017 usr
# 

```
