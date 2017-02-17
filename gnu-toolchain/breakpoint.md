

A call stack for GDB break command processing

```
#0  create_breakpoint (gdbarch=0x1547600, location=location@entry=0x165fea0, cond_string=cond_string@entry=0x0, thread=thread@entry=0, extra_string=extra_string@entry=0x1b2bb1e "", 
    parse_extra=parse_extra@entry=1, tempflag=0, type_wanted=bp_breakpoint, ignore_count=0, pending_break_support=AUTO_BOOLEAN_AUTO, ops=0xb92300 <bkpt_breakpoint_ops>, from_tty=1, enabled=1, 
    internal=0, flags=0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/breakpoint.c:9749
#1  0x00000000004c0a29 in break_command_1 (arg=0x1b2bb1e "", flag=<optimised out>, from_tty=1) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/breakpoint.c:9994
#2  0x0000000000440ae3 in cmd_func (cmd=0x14072a0, args=0x1b2bb12 "start_kernel", from_tty=1) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/cli/cli-decode.c:1913
#3  0x00000000005f3d20 in execute_command (p=<optimised out>, p@entry=0x1b2bb10 "b start_kernel", from_tty=1) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/top.c:674
#4  0x00000000005270bc in command_handler (command=0x1b2bb10 "b start_kernel") at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:590
#5  0x00000000005273d8 in command_line_handler (rl=<optimised out>) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:780
#6  0x00000000005266cc in gdb_rl_callback_handler (rl=0x1b2bb60 "") at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:213
#7  0x0000000000636d8f in rl_callback_read_char () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/readline/callback.c:220
#8  0x00000000005265fe in gdb_rl_callback_read_char_wrapper_noexcept () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:175
#9  0x0000000000526679 in gdb_rl_callback_read_char_wrapper (client_data=<optimised out>) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:192
#10 0x0000000000526bf0 in stdin_event_handler (error=<optimised out>, client_data=0x13b40a0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:518
#11 0x0000000000525a05 in gdb_wait_for_event (block=block@entry=1) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-loop.c:859
#12 0x0000000000525b52 in gdb_do_one_event () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-loop.c:347
#13 0x0000000000525cae in gdb_do_one_event () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-loop.c:304
#14 start_event_loop () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-loop.c:371
#15 0x00000000005766e8 in captured_command_loop (data=data@entry=0x0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/main.c:325
#16 0x0000000000527ce3 in catch_errors (func=func@entry=0x5766c0 <captured_command_loop(void*)>, func_args=func_args@entry=0x0, errstring=errstring@entry=0x7a29b4 "", mask=mask@entry=RETURN_MASK_ALL)
    at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/exceptions.c:236
#17 0x000000000057754e in captured_main (data=0x7ffcfaae52e0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/main.c:1148
#18 gdb_main (args=args@entry=0x7ffcfaae5410) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/main.c:1158
#19 0x00000000004082d5 in main (argc=<optimised out>, argv=<optimised out>) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/gdb.c:32

```
