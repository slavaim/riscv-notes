
The machine level SBI ( Supervisor Binary Interface ) is exported to the Linux kernel by mapping it at the top of the address space.

The mapping is performed by BBL in ```supervisor_vm_init``` defined in ```riscv-tools/riscv-pk/bbl/bbl.c```.

```
  // map SBI at top of vaddr space
  extern char _sbi_end;
  uintptr_t num_sbi_pages = ((uintptr_t)&_sbi_end - DRAM_BASE - 1) / RISCV_PGSIZE + 1;
  assert(num_sbi_pages <= (1 << RISCV_PGLEVEL_BITS));
  for (uintptr_t i = 0; i < num_sbi_pages; i++) {
    uintptr_t idx = (1 << RISCV_PGLEVEL_BITS) - num_sbi_pages + i;
    sbi_pt[idx] = pte_create((DRAM_BASE / RISCV_PGSIZE) + i, PTE_G | PTE_R | PTE_X);
  }
  pte_t* sbi_pte = middle_pt + ((num_middle_pts << RISCV_PGLEVEL_BITS)-1);
  assert(!*sbi_pte);
  *sbi_pte = ptd_create((uintptr_t)sbi_pt >> RISCV_PGSHIFT);
```

You can read more on ```supervisor_vm_init``` here https://github.com/slavaim/riscv-notes/blob/master/bbl/supervisor_vm_init.md . From the code above you can see that the last page ending at ```_sbi_end``` physical address is mapped at the last page of the supervisor virtual address space.

The offsets to SBI entry points are defined in ```riscv-tools/riscv-pk/machine/sbi.S``` as
```
.globl sbi_hart_id; sbi_hart_id = -2048
.globl sbi_num_harts; sbi_num_harts = -2032
.globl sbi_query_memory; sbi_query_memory = -2016
.globl sbi_console_putchar; sbi_console_putchar = -2000
.globl sbi_console_getchar; sbi_console_getchar = -1984
.globl sbi_send_ipi; sbi_send_ipi = -1952
.globl sbi_clear_ipi; sbi_clear_ipi = -1936
.globl sbi_timebase; sbi_timebase = -1920
.globl sbi_shutdown; sbi_shutdown = -1904
.globl sbi_set_timer; sbi_set_timer = -1888
.globl sbi_mask_interrupt; sbi_mask_interrupt = -1872
.globl sbi_unmask_interrupt; sbi_unmask_interrupt = -1856
.globl sbi_remote_sfence_vm; sbi_remote_sfence_vm = -1840
.globl sbi_remote_sfence_vm_range; sbi_remote_sfence_vm_range = -1824
.globl sbi_remote_fence_i; sbi_remote_fence_i = -1808
```

These definitions are offsets from the top of the address space for the SBI trampoline stubs defined in ```riscv-tools/riscv-pk/machine/sbi_entry.S```
```
  # hart_id
  .align 4
  li a7, MCALL_HART_ID
  ecall
  ret

  # num_harts
  .align 4
  lw a0, num_harts
  ret

  # query_memory
  .align 4
  tail __sbi_query_memory
```

The SBI trampoline stubs code start is defined as ```sbi_base``` and is aligned to a page boundary by ```align RISCV_PGSHIFT``` directive. The first ```RISCV_PGSIZE - 2048``` bytes are reserved by ```.skip RISCV_PGSIZE - 2048``` directive so the first instruction starts at 2048 bytes offset from the page top defined as 
```
.align RISCV_PGSHIFT
  .globl sbi_base
sbi_base:

  # TODO: figure out something better to do with this space.  It's not
  # protected from the OS, so beware.
  .skip RISCV_PGSIZE - 2048
```
The end of the section is also aligned at the page boundary and is defined as
```
  .align RISCV_PGSHIFT
  .globl _sbi_end
_sbi_end:
```
 The SBI trampoline stubs section ```.sbi``` is placed at the end of BBL just before the payload by defining the layout in ```riscv-tools/riscv-pk/bbl/bbl.lds``` as
 ```
   .sbi :
  {
    *(.sbi)
  }

  .payload :
  {
    *(.payload)
  }

  _end = .;
 ```

So the ```supervisor_vm_init``` code that maps machine level physical addresses to supervisor virtuall addresses maps the BBL ```.sbi``` section which contains SBI trampoline stubs at the top of the supervisor virtual address space.

Linux kernel access SBI trampoline stubs by a call with offsets defined in ```linux/linux-4.6.2/arch/riscv/kernel/sbi.S``` which is a carbon copy of ```riscv-tools/riscv-pk/machine/sbi.S``` . For example a snippet from Linux kernel entry point ```_start``` defined in  ```linux/linux-4.6.2/arch/riscv/kernel/head.S``` 

```
	/* See if we're the main hart */
	call sbi_hart_id
	bnez a0, .Lsecondary_start
```
This code is translated by GCC to 
```
   0xffffffff80000018 <+24>:	jalr	-2048(zero) # 0xfffffffffffff800
   0xffffffff8000001c <+28>:	bnez	a0,0xffffffff80000054 <_start+84>
```

The address ```0xfffffffffffff800``` is 2048 bytes offset from the top of the virtual address space last page. As we saw above this page is backed by a physical page with SBI trampoline stubs code starting at ```sbi_base``` machine level physical address. The dissassembling shows the SBI trampoline stubs
```
(gdb) x/48i 0xfffffffffffff800
   0xfffffffffffff800:	li	a7,0
   0xfffffffffffff804:	ecall
   0xfffffffffffff808:	ret
   0xfffffffffffff80c:	nop
   0xfffffffffffff810:	auipc	a0,0xfffff
   0xfffffffffffff814:	lw	a0,-1888(a0)
   0xfffffffffffff818:	ret
   0xfffffffffffff81c:	nop
   0xfffffffffffff820:	j	0xffffffffffff92c0
   0xfffffffffffff824:	nop
   0xfffffffffffff828:	nop
   0xfffffffffffff82c:	nop
   0xfffffffffffff830:	li	a7,1
   0xfffffffffffff834:	ecall
   0xfffffffffffff838:	ret
   0xfffffffffffff83c:	nop
   0xfffffffffffff840:	li	a7,2
   0xfffffffffffff844:	ecall
   0xfffffffffffff848:	ret
   0xfffffffffffff84c:	nop
   0xfffffffffffff850:	unimp
   0xfffffffffffff854:	nop
   0xfffffffffffff858:	nop
   0xfffffffffffff85c:	nop
   0xfffffffffffff860:	li	a7,4
   0xfffffffffffff864:	ecall
   0xfffffffffffff868:	ret
   0xfffffffffffff86c:	nop
   0xfffffffffffff870:	li	a7,5
   0xfffffffffffff874:	ecall
   0xfffffffffffff878:	ret
   0xfffffffffffff87c:	nop
   0xfffffffffffff880:	lui	a0,0x989
   0xfffffffffffff884:	addiw	a0,a0,1664
   0xfffffffffffff888:	ret
   0xfffffffffffff88c:	nop
   0xfffffffffffff890:	li	a7,6
   0xfffffffffffff894:	ecall
   0xfffffffffffff898:	nop
   0xfffffffffffff89c:	nop
   0xfffffffffffff8a0:	li	a7,7
   0xfffffffffffff8a4:	ecall
   0xfffffffffffff8a8:	ret
   0xfffffffffffff8ac:	nop
   0xfffffffffffff8b0:	j	0xffffffffffff92f8
   0xfffffffffffff8b4:	nop
   0xfffffffffffff8b8:	nop
   0xfffffffffffff8bc:	nop
```
