
The machine level SBI is exported to linux by mapping it at the top of the address space.
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

You can read more on ```supervisor_vm_init``` here https://github.com/slavaim/riscv-notes/blob/master/bbl/supervisor_vm_init.md . From the code above you can see that the last page ending at ```_sbi_end``` physical address is mapped at the last page of the virtual address space.

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

These definitions are offsets from the top of the address space for the SBI stubs 3 instructuions stubs aligned on 4 bytes defined in ```riscv-tools/riscv-pk/machine/sbi_entry.S```
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

The SBI stubs code start is defined as ```sbi_base``` and is aligned to a page boundary by ```align RISCV_PGSHIFT``` directive. The first ```RISCV_PGSIZE - 2048``` bytes are reserved by ```.skip RISCV_PGSIZE - 2048``` directive so the first instruction starts at 2048 bytes offset from the page top. 
```
.align RISCV_PGSHIFT
  .globl sbi_base
sbi_base:

  # TODO: figure out something better to do with this space.  It's not
  # protected from the OS, so beware.
  .skip RISCV_PGSIZE - 2048
```
 The SBI stubs section ```.sbi``` is placed at the end of BBL just before the payload by defining the layout in ```riscv-tools/riscv-pk/bbl/bbl.lds``` as
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

So the ```supervisor_vm_init``` code that maps machine level physical addresses to supervisor virtuall addresses maps the BBL .sbi section which contains SBI stubs at the top of the address space.
