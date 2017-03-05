
##Dissecting supervisor_vm_init routine.

The function builds page table structures to map BBL payload to supervisor mode. The function operates in machine level physical address space. You should not be fooled by presence of supervisor virtual addresses as they are adjusted to machine level physical address before being accessed. 

```
static void supervisor_vm_init()
{
  uintptr_t highest_va = DRAM_BASE - first_free_paddr;
  mem_size = MIN(mem_size, highest_va - info.first_user_vaddr) & -MEGAPAGE_SIZE;

  pte_t* sbi_pt = (pte_t*)(info.first_vaddr_after_user + info.load_offset);
  memset(sbi_pt, 0, RISCV_PGSIZE);
  pte_t* middle_pt = (void*)sbi_pt + RISCV_PGSIZE;
#if __riscv_xlen == 32
  size_t num_middle_pts = 1;
  pte_t* root_pt = middle_pt;
  memset(root_pt, 0, RISCV_PGSIZE);
#else
  size_t num_middle_pts = (-info.first_user_vaddr - 1) / GIGAPAGE_SIZE + 1;
  pte_t* root_pt = (void*)middle_pt + num_middle_pts * RISCV_PGSIZE;
  memset(middle_pt, 0, (num_middle_pts + 1) * RISCV_PGSIZE);
  for (size_t i = 0; i < num_middle_pts; i++)
    root_pt[(1<<RISCV_PGLEVEL_BITS)-num_middle_pts+i] = ptd_create(((uintptr_t)middle_pt >> RISCV_PGSHIFT) + i);
#endif

  for (uintptr_t vaddr = info.first_user_vaddr, paddr = vaddr + info.load_offset, end = info.first_vaddr_after_user;
       paddr < DRAM_BASE + mem_size; vaddr += MEGAPAGE_SIZE, paddr += MEGAPAGE_SIZE) {
    int l2_shift = RISCV_PGLEVEL_BITS + RISCV_PGSHIFT;
    size_t l2_idx = (info.first_user_vaddr >> l2_shift) & ((1 << RISCV_PGLEVEL_BITS)-1);
    l2_idx += ((vaddr - info.first_user_vaddr) >> l2_shift);
    middle_pt[l2_idx] = pte_create(paddr >> RISCV_PGSHIFT, PTE_G | PTE_R | PTE_W | PTE_X);
  }

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

  mb();
  root_page_table = root_pt;
  write_csr(sptbr, (uintptr_t)root_pt >> RISCV_PGSHIFT);
}
```

###Commentary.

```
uintptr_t highest_va = DRAM_BASE - first_free_paddr;
```
The above operation calculates the highest supervisor VA(virtual address) ```highest_va``` value. ```DRAM_BASE``` is less than ```first_free_paddr``` which is the address of the first free megapage after BBL+payload was loaded to DRAM starting at DRAM_BASE machine level address. On my test system ```DRAM_BASE = 0x80000000``` and ```first_free_paddr = 0x82800000``` these are machine level physical adresses as CPU starts at machine level mode. The difference is a negative number which in two's complement arithmetic gives the valid virtual address at the top of the 64 bit address range ```highest_va = 0xfffffffffd800000``` for supervisor mode. This leaves intact a top VA range in supervider mode thus preserving the machine level code which is mapped at this range, see below.

The info structure describes the payload with an ELF header. Typical values on my system for the Linux kernel as a payload as they are shown by GDB print command are
```
(gdb) p/x info
$6 = {entry = 0xffffffff80000000, first_user_vaddr = 0xffffffff80000000, first_vaddr_after_user = 0xffffffff803b2000, load_offset = 0x102800000}
```

The memory size available for machine level mode is
```
(gdb) p/x mem_size
$16 = 0x100000000
```
It should be adjusted for supervisor. The memory size available for supervisor is calculated as
```
mem_size = MIN(mem_size, highest_va - info.first_user_vaddr) & -MEGAPAGE_SIZE;
```
On my system this value is
```
(gdb) p/x $a5
$11 = 0x7d800000
```

Then the special page table structure is allocated. This structure is used to map the BBL and its payload at the top of the address space inaccessible bthrough the supervisor page table structure ```root_pt``` which will be discussed shortly.
```
pte_t* sbi_pt = (pte_t*)(info.first_vaddr_after_user + info.load_offset);
```
The ```sbi_pt``` value on my machine is
```
(gdb) p/x $s1
$15 = 0x82bb2000
```
As you can see the CPU works with machine level addresses while ```info.first_vaddr_after_user``` is a supervisor virtual address. The ```info.load_offset``` value is used to adjust the supervisor virtual address to machine level physical address.

Then the real supervisor page table address is calculated by allocating a middle/directory table just after ```sbi_pt```
```
pte_t* middle_pt = (void*)sbi_pt + RISCV_PGSIZE;
```
Then the root page table pointer is initialized.
```
#if __riscv_xlen == 32
  size_t num_middle_pts = 1;
  pte_t* root_pt = middle_pt;
  memset(root_pt, 0, RISCV_PGSIZE);
#else
  size_t num_middle_pts = (-info.first_user_vaddr - 1) / GIGAPAGE_SIZE + 1;
  pte_t* root_pt = (void*)middle_pt + num_middle_pts * RISCV_PGSIZE;
  memset(middle_pt, 0, (num_middle_pts + 1) * RISCV_PGSIZE);
  for (size_t i = 0; i < num_middle_pts; i++)
    root_pt[(1<<RISCV_PGLEVEL_BITS)-num_middle_pts+i] = ptd_create(((uintptr_t)middle_pt >> RISCV_PGSHIFT) + i);
#endif
```

The supervisor page table structure is then initialized to map supervisor virtual addresses to machine level physical addresses. Look how ```info.load_offset``` is used again to translate supervisor virtual address to machine level physical address.

```
  for (uintptr_t vaddr = info.first_user_vaddr, paddr = vaddr + info.load_offset, end = info.first_vaddr_after_user;
       paddr < DRAM_BASE + mem_size; vaddr += MEGAPAGE_SIZE, paddr += MEGAPAGE_SIZE) {
    int l2_shift = RISCV_PGLEVEL_BITS + RISCV_PGSHIFT;
    size_t l2_idx = (info.first_user_vaddr >> l2_shift) & ((1 << RISCV_PGLEVEL_BITS)-1);
    l2_idx += ((vaddr - info.first_user_vaddr) >> l2_shift);
    middle_pt[l2_idx] = pte_create(paddr >> RISCV_PGSHIFT, PTE_G | PTE_R | PTE_W | PTE_X);
  }
```
The machine level BBL code with payload is remapped at the top of the range reserved above ```highest_va``` by a separate page table structure rooted at ```sbi_pt```. The BBL has been loaded at ```DRAM_BASE``` physical address. This address range is mapped as a read only range for supervisor mode. The PTE are also marked as global so they are visible in all address spaces. This address range is inaccessible through the suppervisor page table structure initialized in ```middle_pt```.

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

Before returning to a caller the function sets page table base register for supervisor virtual address translation. The memory barrier guaranties that all memory writes has completed so the page table is in a consistent state.
```
  mb();
  root_page_table = root_pt;
  write_csr(sptbr, (uintptr_t)root_pt >> RISCV_PGSHIFT);
```
