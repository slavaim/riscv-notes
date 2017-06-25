
The kernel printf

```
#3  0xffffffff8002e460 in __kernel_serial_write (str=0xffffffff800301d0 "Available physical memory: %ludMB\n", len=27) at kernel/lib/io/console.c:45
#4  0xffffffff8002e594 in __kernel_stdout_write (str=0xffffffff800301d0 "Available physical memory: %ludMB\n", len=27) at kernel/lib/io/console.c:82
#5  0xffffffff8002e5d0 in __printf_output_func (s=0xffffffff800301d0 "Available physical memory: %ludMB\n", len=27, state=0x0) at kernel/lib/io/console.c:150
#6  0xffffffff8002c048 in _printf_engine (out=0xffffffff8002e5a8 <__printf_output_func>, state=0x0, fmt=0xffffffff800301ec "ludMB\n", ap=0xffffffff8003bf88 <_kstack+3896>) at kernel/lib/libc/printf.c:435
#7  0xffffffff8002cb28 in _printf (fmt=0xffffffff800301d0 "Available physical memory: %ludMB\n") at kernel/lib/libc/stdio.c:45
```

A kernel logger thread
```
#0  0xffffffff80002070 in platform_dputs (str=0xffffffff800f15cc <spin_lock_save+48> "\023", len=18446744071580226816)
#1  0xffffffff800f1660 in __kernel_serial_write (str=0xffffffff811516b8 "[1266874889.709] 00000.00000> Available physical memory: \n", len=58) at kernel/lib/io/console.c:47
#2  0xffffffff80013178 in debuglog_dumper (arg=0x0) at kernel/lib/debuglog/debuglog.c:298
#3  0xffffffff8000b9f4 in initial_thread_func () at kernel/kernel/thread.c:84
#4  0xffffffff8000b9b4 in init_thread_struct (t=0xffffffff8114f480, name=0x0) at kernel/kernel/thread.c:72
```
