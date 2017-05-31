
The library code

```
const
unsigned char __clz_tab[] =
{
  0,1,2,2,3,3,3,3,4,4,4,4,4,4,4,4,5,5,5,5,5,5,5,5,5,5,5,5,5,5,5,5,
  6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,
  7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,
  7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,7,
  8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,
  8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,
  8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,
  8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,
};

long foo(long x)
{
    return __clz_tab[x % (sizeof(__clz_tab)/sizeof(__clz_tab[0]))];
}
```

Build the shared(*.so) and static(*.o) library.

```
slava@slava-P34V2:/work/risc-v/musl-riscv/tmp$ riscv64-unknown-linux-gnu-gcc  -shared -ffreestanding -nostdlib -fPIC  -o relocation.so relocation.c
slava@slava-P34V2:/work/risc-v/musl-riscv/tmp$ riscv64-unknown-linux-gnu-gcc  -c -ffreestanding -nostdlib -fPIC  -o relocation.o relocation.c
```

The shared library has a ```.got``` (Global Offset Table) section to reference the ```__clz_tab``` array. The ```.got``` section is adjusted by the loader with the relocation data from the ```.rela.dyn``` section.

```
slava@slava-P34V2:/work/risc-v/musl-riscv/tmp$ riscv64-unknown-linux-gnu-objdump -hr relocation.so

relocation.so:     file format elf64-littleriscv

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .hash         00000030  00000000000000e8  00000000000000e8  000000e8  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  1 .dynsym       000000a8  0000000000000118  0000000000000118  00000118  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  2 .dynstr       00000027  00000000000001c0  00000000000001c0  000001c0  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  3 .rela.dyn     00000018  00000000000001e8  00000000000001e8  000001e8  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  4 .text         00000038  0000000000000200  0000000000000200  00000200  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
  5 .rodata       00000100  0000000000000238  0000000000000238  00000238  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  6 .dynamic      000000e0  0000000000001338  0000000000001338  00000338  2**3
                  CONTENTS, ALLOC, LOAD, DATA
  7 .got          00000020  0000000000001418  0000000000001418  00000418  2**3
                  CONTENTS, ALLOC, LOAD, DATA
  8 .comment      00000011  0000000000000000  0000000000000000  00000438  2**0
                  CONTENTS, READONLY
                  
slava@slava-P34V2:/work/risc-v/musl-riscv/tmp$ riscv64-unknown-linux-gnu-objdump --disassemble relocation.so

relocation.so:     file format elf64-littleriscv


Disassembly of section .text:

0000000000000200 <foo>:
 200:	fe010113          	addi	sp,sp,-32
 204:	00813c23          	sd	s0,24(sp)
 208:	02010413          	addi	s0,sp,32
 20c:	fea43423          	sd	a0,-24(s0)
 210:	fe843783          	ld	a5,-24(s0)
 214:	0ff7f793          	andi	a5,a5,255
 218:	00001717          	auipc	a4,0x1
 21c:	21873703          	ld	a4,536(a4) # 1430 <_GLOBAL_OFFSET_TABLE_+0x8>
 220:	00f707b3          	add	a5,a4,a5
 224:	0007c783          	lbu	a5,0(a5)
 228:	00078513          	mv	a0,a5
 22c:	01813403          	ld	s0,24(sp)
 230:	02010113          	addi	sp,sp,32
 234:	00008067          	ret
 
slava@slava-P34V2:/work/risc-v/musl-riscv/tmp$ riscv64-unknown-linux-gnu-readelf -r  relocation.so

Relocation section '.rela.dyn' at offset 0x1e8 contains 1 entries:
  Offset          Info           Type           Sym. Value    Sym. Name + Addend
000000001430  000300000002 R_RISCV_64        0000000000000238 __clz_tab + 0
slava@slava-P34V2:/work/risc-v/musl-riscv/tmp$ riscv64-unknown-linux-gnu-objdump -hr relocation.o

relocation.o:     file format elf64-littleriscv

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .text         00000038  0000000000000000  0000000000000000  00000040  2**2
                  CONTENTS, ALLOC, LOAD, RELOC, READONLY, CODE
  1 .data         00000000  0000000000000000  0000000000000000  00000078  2**0
                  CONTENTS, ALLOC, LOAD, DATA
  2 .bss          00000000  0000000000000000  0000000000000000  00000078  2**0
                  ALLOC
  3 .rodata       00000100  0000000000000000  0000000000000000  00000078  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  4 .comment      00000012  0000000000000000  0000000000000000  00000178  2**0
                  CONTENTS, READONLY
RELOCATION RECORDS FOR [.text]:
OFFSET           TYPE              VALUE 
0000000000000018 R_RISCV_GOT_HI20  __clz_tab
000000000000001c R_RISCV_PCREL_LO12_I  .L0 
```

The static library also has a ```.got``` (Global Offset Table) section to reference the ```__clz_tab``` array. The ```.got``` section is adjusted by the loader with the relocation data from the ```.rela.dyn``` section.

```
slava@slava-P34V2:/work/risc-v/musl-riscv/tmp$ riscv64-unknown-linux-gnu-objdump -hr relocation.o

relocation.o:     file format elf64-littleriscv

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .text         00000038  0000000000000000  0000000000000000  00000040  2**2
                  CONTENTS, ALLOC, LOAD, RELOC, READONLY, CODE
  1 .data         00000000  0000000000000000  0000000000000000  00000078  2**0
                  CONTENTS, ALLOC, LOAD, DATA
  2 .bss          00000000  0000000000000000  0000000000000000  00000078  2**0
                  ALLOC
  3 .rodata       00000100  0000000000000000  0000000000000000  00000078  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  4 .comment      00000012  0000000000000000  0000000000000000  00000178  2**0
                  CONTENTS, READONLY
RELOCATION RECORDS FOR [.text]:
OFFSET           TYPE              VALUE 
0000000000000018 R_RISCV_GOT_HI20  __clz_tab
000000000000001c R_RISCV_PCREL_LO12_I  .L0 

slava@slava-P34V2:/work/risc-v/musl-riscv/tmp$ riscv64-unknown-linux-gnu-objdump --disassemble relocation.o

relocation.o:     file format elf64-littleriscv


Disassembly of section .text:

0000000000000000 <foo>:
   0:	fe010113          	addi	sp,sp,-32
   4:	00813c23          	sd	s0,24(sp)
   8:	02010413          	addi	s0,sp,32
   c:	fea43423          	sd	a0,-24(s0)
  10:	fe843783          	ld	a5,-24(s0)
  14:	0ff7f793          	andi	a5,a5,255

0000000000000018 <.L0 >:
  18:	00000717          	auipc	a4,0x0
  1c:	00073703          	ld	a4,0(a4) # 18 <.L0 >
  20:	00f707b3          	add	a5,a4,a5
  24:	0007c783          	lbu	a5,0(a5)
  28:	00078513          	mv	a0,a5
  2c:	01813403          	ld	s0,24(sp)
  30:	02010113          	addi	sp,sp,32
  34:	00008067          	ret
  
slava@slava-P34V2:/work/risc-v/musl-riscv/tmp$ riscv64-unknown-linux-gnu-readelf -r  relocation.o

Relocation section '.rela.text' at offset 0x2a8 contains 2 entries:
  Offset          Info           Type           Sym. Value    Sym. Name + Addend
000000000018  000800000014 R_RISCV_GOT_HI20  0000000000000000 __clz_tab + 0
00000000001c  000600000018 R_RISCV_PCREL_LO1 0000000000000018 .L0  + 0
```
