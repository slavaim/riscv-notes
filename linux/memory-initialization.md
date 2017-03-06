
The kernel is started with virtual memory initialized by machine level bootloader BBL. The more detailed description can be found here https://github.com/slavaim/riscv-notes/blob/master/bbl/supervisor_vm_init.md .

The kernel start offset is defined in ```linux/linux-4.6.2/arch/riscv/include/asm/page.h```
```
/*
 * PAGE_OFFSET -- the first address of the first page of memory.
 * When not using MMU this corresponds to the first free page in
 * physical memory (aligned on a page boundary).
 */
#ifdef CONFIG_64BIT
#define PAGE_OFFSET		_AC(0xffffffff80000000,UL)
#else
#define PAGE_OFFSET		_AC(0xc0000000,UL)
#endif
```

BBL initializes virtual memory for supervisor mode, maps the Linux kernel at PAGE_OFFSET, sets ```sptbr``` register value to a root page table physical address, switches to the supervisor mode with ```$pc``` set to the entry point ```_start```. BBL does this in ```enter_supervisor_mode``` function defined in ```/work/risc-v/riscv-tools/riscv-pk/machine/minit.c```

```
void enter_supervisor_mode(void (*fn)(uintptr_t), uintptr_t stack)
{
  uintptr_t mstatus = read_csr(mstatus);
  mstatus = INSERT_FIELD(mstatus, MSTATUS_MPP, PRV_S);
  mstatus = INSERT_FIELD(mstatus, MSTATUS_MPIE, 0);
  write_csr(mstatus, mstatus);
  write_csr(mscratch, MACHINE_STACK_TOP() - MENTRY_FRAME_SIZE);
  write_csr(mepc, fn);
  write_csr(sptbr, (uintptr_t)root_page_table >> RISCV_PGSHIFT);
  asm volatile ("mv a0, %0; mv sp, %0; mret" : : "r" (stack));
  __builtin_unreachable();
}
```

The important difference between RISC-V case and many other CPUs( e.g. x86 )is that Linux kernel's entry point is called with virtual memory initialized by boot loader executing at higher privilege mode.

The Linux kernel retrieves the page table allocated and initialized by BBL and saves its virtual address at the master kernel Page Tables ```init_mm.pgd``` by reading a physical address from the ```sptbr``` register and converting it to a virtual address. The snippet is from ```linux/linux-4.6.2/arch/riscv/mm/init.c```
```
void __init paging_init(void)
{
	init_mm.pgd = (pgd_t *)pfn_to_virt(csr_read(sptbr));
  ....
}
```

