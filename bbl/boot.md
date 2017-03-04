After reset a CPU fetches the first instruction from 0x80000000.

```
(gdb) info registers
ra             0x0000000000000000	0
sp             0x0000000000000000	0
gp             0x0000000000000000	0
tp             0x0000000000000000	0
t0             0x0000000080000000	2147483648
t1             0x0000000000000000	0
t2             0x0000000000000000	0
fp             0x0000000000000000	0
s1             0x0000000000000000	0
a0             0x0000000000000000	0
a1             0x0000000000000000	0
a2             0x0000000000000000	0
a3             0x0000000000000000	0
a4             0x0000000000000000	0
a5             0x0000000000000000	0
a6             0x0000000000000000	0
a7             0x0000000000000000	0
s2             0x0000000000000000	0
s3             0x0000000000000000	0
s4             0x0000000000000000	0
s5             0x0000000000000000	0
s6             0x0000000000000000	0
s7             0x0000000000000000	0
s8             0x0000000000000000	0
s9             0x0000000000000000	0
s10            0x0000000000000000	0
s11            0x0000000000000000	0
t3             0x0000000000000000	0
t4             0x0000000000000000	0
t5             0x0000000000000000	0
t6             0x0000000000000000	0
pc             0x0000000080000000	2147483648
```

BBL assigns 0x80000000 address to ```reset_vector``` by placing it in the beginning of the .text.init section which is linked to 0x80000000 .

From riscv-tools/riscv-pk/machine/mentry.S
```
  .section .text.init,"ax",@progbits
  .globl reset_vector
reset_vector:
  j do_reset
```

From riscv-tools/riscv-pk/pk/pk.lds
```
  /* Begining of code and text segment */
  . = 0x80000000;
  _ftext = .;
  PROVIDE( eprol = . );

  .text :
  {
    *(.text.init)
  }
```

