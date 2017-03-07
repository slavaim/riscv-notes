On boot the kernel has the following memory areas
- vmlinux ELF code and data sections mapped by the bootloader
- the page tables for virtual memory support created by the bootloader
- initial stack

The pages used by the above regions must be marked as reserved so they are not used for the following allocations.

As shown here https://github.com/slavaim/riscv-notes/blob/master/linux/memory-initialization.md the kernel makes the following calls for memory reservation.

```
	memblock_reserve(info.base, __pa(_end) - info.base);
	reserve_boot_page_table(pfn_to_virt(csr_read(sptbr)));
```

The first call is to reserve the area from ```&_start``` to ```&_end`` , this area is defined in the following linker script.

```
SECTIONS
{
	/* Beginning of code and text segment */
	. = LOAD_OFFSET;
	_start = .;
	__init_begin = .;
	HEAD_TEXT_SECTION
	INIT_TEXT_SECTION(PAGE_SIZE)
	INIT_DATA_SECTION(16)
	/* we have to discard exit text and such at runtime, not link time */
	.exit.text :
	{
		EXIT_TEXT
	}
	.exit.data :
	{
		EXIT_DATA
	}
	PERCPU_SECTION(L1_CACHE_BYTES)
	__init_end = .;

	.text : {
		_text = .;
		_stext = .;
		TEXT_TEXT
		SCHED_TEXT
		LOCK_TEXT
		KPROBES_TEXT
		ENTRY_TEXT
		IRQENTRY_TEXT
		*(.fixup)
		_etext = .;
	}

	/* Start of data section */
	_sdata = .;
	RO_DATA_SECTION(PAGE_SIZE)
	RW_DATA_SECTION(0x40, PAGE_SIZE, THREAD_SIZE)
	.sdata : {
		_gp = . + 0x800;
		*(.sdata*)
	}
	.srodata : {
		*(.srodata*)
	}
	/* End of data section */
	_edata = .;

	BSS_SECTION(0x20, 0, 0x20)

	EXCEPTION_TABLE(0x10)
	NOTES

	.rel.dyn : {
		*(.rel.dyn*)
	}

	_end = .;

	STABS_DEBUG
	DWARF_DEBUG

	DISCARDS
}
```

As you can see this area encompasses all kernel code and data excluding debug information. This area starts at ```ffffffff80000000```. You can easily find the end address from the System.map file. The vaues for my test kernel are 
```
ffffffff80000000 T _start
ffffffff803b10b4 R _end
```

The second call reserves the initial page table pages.  
Where is a stack reservation? The stack is reserved by the first call as the initial stack is allocated from the kernel data section. The initial stack is staically allocated as ```init_thread_union.stack``` . The ```init_thread_union``` has the following type definition in ```linux/linux-4.6.2/include/linux/sched.h```
```
union thread_union {
	struct thread_info thread_info;
	unsigned long stack[THREAD_SIZE/sizeof(long)];
};
```
For my test kernel the address of the init_thread_union is again extracted from System.map as
```
ffffffff8035e000 D init_thread_union
```
As you can see it is in the range of the region ```[&_start,&_end)``` and is in the data section.   
The stack register is set on boot by the kernel entry routine ```_start``` defined in ```linux/linux-4.6.2/arch/riscv/kernel/head.S```
```
__INIT
ENTRY(_start)
...
	/* Initialize stack pointer */
	la sp, init_thread_union + THREAD_SIZE
	/* Initialize current task_struct pointer */
	la tp, init_task
 ...
 END(_start)
```
