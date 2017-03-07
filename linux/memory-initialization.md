
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

BBL initializes virtual memory for supervisor mode, maps the Linux kernel at PAGE_OFFSET, sets ```sptbr``` register value to a root page table physical address, switches to the supervisor mode with ```$pc``` set to the entry point ```_start```. BBL does this in ```enter_supervisor_mode``` function defined in ```riscv-tools/riscv-pk/machine/minit.c```

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

The memory management is initialized inside ```setup_arch``` routine defined in ```linux/linux-4.6.2/arch/riscv/kernel/setup.c```, below only memory management relevant part of the function is shown

```
void __init setup_arch(char **cmdline_p)
{
...
	init_mm.start_code = (unsigned long) _stext;
	init_mm.end_code   = (unsigned long) _etext;
	init_mm.end_data   = (unsigned long) _edata;
	init_mm.brk        = (unsigned long) _end;

	setup_bootmem();
	....
	paging_init();
	....
}
```

The first function being called is ```setup_bootmem```

```
static void __init setup_bootmem(void)
{
	unsigned long ret;
	memory_block_info info;

	ret = sbi_query_memory(0, &info);
	BUG_ON(ret != 0);
	BUG_ON((info.base & ~PMD_MASK) != 0);
	BUG_ON((info.size & ~PMD_MASK) != 0);
	pr_info("Available physical memory: %ldMB\n", info.size >> 20);

	/* The kernel image is mapped at VA=PAGE_OFFSET and PA=info.base */
	va_pa_offset = PAGE_OFFSET - info.base;
	pfn_base = PFN_DOWN(info.base);

	if ((mem_size != 0) && (mem_size < info.size)) {
		memblock_enforce_memory_limit(mem_size);
		info.size = mem_size;
		pr_notice("Physical memory usage limited to %lluMB\n",
			(unsigned long long)(mem_size >> 20));
	}
	set_max_mapnr(PFN_DOWN(info.size));
	max_low_pfn = PFN_DOWN(info.base + info.size);

#ifdef CONFIG_BLK_DEV_INITRD
	setup_initrd();
#endif /* CONFIG_BLK_DEV_INITRD */

	memblock_reserve(info.base, __pa(_end) - info.base);
	reserve_boot_page_table(pfn_to_virt(csr_read(sptbr)));
	memblock_allow_resize();
}
```

The Linux kernel queries the available memory size in ```setup_bootmem``` by invoking SBI interface's ```sbi_query_memory``` which results in a call to ```__sbi_query_memory``` BBL routine executed (suprisingly) in supervisor mode as SBI has been mapped to the supervisor virtual address space and  ```ecall``` instruction is not invoked for ```sbi_query_memory```
```
uintptr_t __sbi_query_memory(uintptr_t id, memory_block_info *p)
{
  if (id == 0) {
    p->base = first_free_paddr;
    p->size = mem_size + DRAM_BASE - p->base;
    return 0;
  }

  return -1;
}
```
More about SBI can be found here https://github.com/slavaim/riscv-notes/blob/master/bbl/sbi-to-linux.md .  
The kernel reserves the pages occupied by the kernel with a call to ```memblock_reserve(info.base, __pa(_end) - info.base);``` . Then a call to ```reserve_boot_page_table(pfn_to_virt(csr_read(sptbr)));``` reserves the pages occupied by the page table allocated by the bootloader(BBL).The Linux kernel retrieves the page table allocated and initialized by BBL by reading a physical address from the ```sptbr``` register and converting it to a virtual address. The page table virtual address is also saved at the master kernel Page Tables ```init_mm.pgd```. The snippet is from ```linux/linux-4.6.2/arch/riscv/mm/init.c```
```
void __init paging_init(void)
{
	init_mm.pgd = (pgd_t *)pfn_to_virt(csr_read(sptbr));
  ....
}
```
