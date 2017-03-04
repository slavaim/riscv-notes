
After reset a CPU fetches the instruction from DEFAULT_RSTVEC = 0x00001000.
For example below is QEMU CPU reset emulation from riscv-qemu/target-riscv/cpu.c
```
static void riscv_cpu_reset(CPUState *s)
{
    RISCVCPU *cpu = RISCV_CPU(s);
    RISCVCPUClass *mcc = RISCV_CPU_GET_CLASS(cpu);
    CPURISCVState *env = &cpu->env;
    CPUState *cs = CPU(cpu);

    mcc->parent_reset(s);
#ifndef CONFIG_USER_ONLY
    tlb_flush(s, 1);
    env->priv = PRV_M;
    env->mtvec = DEFAULT_MTVEC;
#endif
    env->pc = DEFAULT_RSTVEC;
    cs->exception_index = EXCP_NONE;
}
```

The 0x00001000 address is mapped to ROM with a trampoline code to 0x80000000. AUIPC moves its immediate value 12 bits to the left and adds to the current PC , so t0 = 0(x7ffff<<12)+ 0x1000 = 0x80000000

```
(gdb) x/10i 0x1000
   0x1000:	auipc	t0,0x7ffff
   0x1004:	jr	t0
```

The 0x80000000 address is a start of DMA. Below are definitions that are the same for the both QEMU and spike simulator.

```
#define DEFAULT_RSTVEC     0x00001000
#define DRAM_BASE          0x80000000
```

After ```jr	t0``` has been execute the register content is as follows ( t0 and pc are equal )

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

