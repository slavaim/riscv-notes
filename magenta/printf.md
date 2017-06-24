
```
#3  0xffffffff8002e460 in __kernel_serial_write (str=0xffffffff800301d0 "Available physical memory: %ludMB\n", len=27) at kernel/lib/io/console.c:45
#4  0xffffffff8002e594 in __kernel_stdout_write (str=0xffffffff800301d0 "Available physical memory: %ludMB\n", len=27) at kernel/lib/io/console.c:82
#5  0xffffffff8002e5d0 in __printf_output_func (s=0xffffffff800301d0 "Available physical memory: %ludMB\n", len=27, state=0x0) at kernel/lib/io/console.c:150
#6  0xffffffff8002c048 in _printf_engine (out=0xffffffff8002e5a8 <__printf_output_func>, state=0x0, fmt=0xffffffff800301ec "ludMB\n", ap=0xffffffff8003bf88 <_kstack+3896>) at kernel/lib/libc/printf.c:435
#7  0xffffffff8002cb28 in _printf (fmt=0xffffffff800301d0 "Available physical memory: %ludMB\n") at kernel/lib/libc/stdio.c:45
```
