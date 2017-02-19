

A call stack for GDB break command processing

```
#0  create_breakpoints_sal (gdbarch=0x1547600, canonical=0x7ffcfaae4f00, cond_string=0x0, extra_string=0x0, type=bp_breakpoint, disposition=disp_donttouch, thread=-1, task=0, ignore_count=0, 
    ops=0xb92300 <bkpt_breakpoint_ops>, from_tty=1, enabled=1, internal=0, flags=0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/breakpoint.c:9439
#1  0x00000000004c058a in create_breakpoint (gdbarch=0x1547600, location=location@entry=0x1a1cb10, cond_string=cond_string@entry=0x0, thread=-1, thread@entry=0, extra_string=0x0, 
    extra_string@entry=0x1a01ace "", parse_extra=parse_extra@entry=1, tempflag=0, type_wanted=bp_breakpoint, ignore_count=0, pending_break_support=AUTO_BOOLEAN_AUTO, ops=0xb92300 <bkpt_breakpoint_ops>, 
    from_tty=1, enabled=1, internal=0, flags=0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/breakpoint.c:9886
#2  0x00000000004c0a29 in break_command_1 (arg=0x1a01ace "", flag=<optimised out>, from_tty=1) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/breakpoint.c:9994
#3  0x0000000000440ae3 in cmd_func (cmd=0x14072a0, args=0x1a01ac2 "start_kernel", from_tty=1) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/cli/cli-decode.c:1913
#4  0x00000000005f3d20 in execute_command (p=<optimised out>, p@entry=0x1a01ac0 "b start_kernel", from_tty=1) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/top.c:674
#5  0x00000000005270bc in command_handler (command=0x1a01ac0 "b start_kernel") at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:590
#6  0x00000000005273d8 in command_line_handler (rl=<optimised out>) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:780
#7  0x00000000005266cc in gdb_rl_callback_handler (rl=0x1b2bb10 "") at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:213
#8  0x0000000000636d8f in rl_callback_read_char () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/readline/callback.c:220
#9  0x00000000005265fe in gdb_rl_callback_read_char_wrapper_noexcept () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:175
#10 0x0000000000526679 in gdb_rl_callback_read_char_wrapper (client_data=<optimised out>) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:192
#11 0x0000000000526bf0 in stdin_event_handler (error=<optimised out>, client_data=0x13b40a0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:518
#12 0x0000000000525a05 in gdb_wait_for_event (block=block@entry=1) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-loop.c:859
#13 0x0000000000525b52 in gdb_do_one_event () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-loop.c:347
#14 0x0000000000525cae in gdb_do_one_event () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-loop.c:304
#15 start_event_loop () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-loop.c:371
#16 0x00000000005766e8 in captured_command_loop (data=data@entry=0x0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/main.c:325
#17 0x0000000000527ce3 in catch_errors (func=func@entry=0x5766c0 <captured_command_loop(void*)>, func_args=func_args@entry=0x0, errstring=errstring@entry=0x7a29b4 "", mask=mask@entry=RETURN_MASK_ALL)
    at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/exceptions.c:236
#18 0x000000000057754e in captured_main (data=0x7ffcfaae52e0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/main.c:1148
#19 gdb_main (args=args@entry=0x7ffcfaae5410) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/main.c:1158
#20 0x00000000004082d5 in main (argc=<optimised out>, argv=<optimised out>) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/gdb.c:32

```
An exception on breakpoint for QEMU

```
#0  0x0000000000710acd in __cxa_throw ()
#1  0x00000000004d729c in throw_exception_cxx (exception=...) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/common/common-exceptions.c:303
#2  0x00000000004d7325 in throw_exception (exception=...) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/common/common-exceptions.c:317
#3  throw_it(return_reason, errors, const char *, typedef __va_list_tag __va_list_tag *) (reason=reason@entry=RETURN_ERROR, error=<optimised out>, fmt=<optimised out>, ap=ap@entry=0x7ffcfaae49e0)
    at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/common/common-exceptions.c:373
#4  0x00000000004d7556 in throw_verror (error=<optimised out>, fmt=<optimised out>, ap=ap@entry=0x7ffcfaae49e0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/common/common-exceptions.c:379
#5  0x00000000004d7614 in throw_error (error=error@entry=NOT_AVAILABLE_ERROR, fmt=fmt@entry=0x7eed80 "Register %d is not available")
    at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/common/common-exceptions.c:394
#6  0x0000000000533a59 in frame_unwind_register (frame=<optimised out>, regnum=834, buf=<optimised out>) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/frame.c:1167
#7  0x0000000000533c2c in frame_unwind_register_unsigned (frame=0x1b2aab0, regnum=regnum@entry=834) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/frame.c:1275
#8  0x0000000000533c6c in get_frame_register_unsigned (frame=<optimised out>, regnum=regnum@entry=834) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/frame.c:1282
#9  0x00000000004097f2 in riscv_breakpoint_kind_from_pc (gdbarch=0x1547600, pcptr=<optimised out>) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/riscv-tdep.c:170
#10 0x00000000004a50b1 in default_breakpoint_from_pc (gdbarch=0x1547600, pcptr=<optimised out>, lenptr=0x7ffcfaae4bac) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/arch-utils.c:847
#11 0x00000000004b8962 in program_breakpoint_here_p (gdbarch=<optimised out>, address=18446744071562069744) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/breakpoint.c:9087
#12 0x00000000004b8b0c in bp_loc_is_permanent (loc=0x152c5f0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/breakpoint.c:9130
#13 add_location_to_breakpoint (b=b@entry=0x1531f70, sal=sal@entry=0x7ffcfaae4c40) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/breakpoint.c:9067
#14 0x00000000004b8b7d in init_raw_breakpoint (b=0x1531f70, gdbarch=<optimised out>, sal=..., bptype=bp_breakpoint, ops=<optimised out>)
    at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/breakpoint.c:7574
#15 0x00000000004b9acf in init_breakpoint_sal (b=b@entry=0x1531f70, gdbarch=gdbarch@entry=0x1547600, location=location@entry=0x1a1cb10, filter=filter@entry=0x0, cond_string=0x0, extra_string=0x0, 
    type=bp_breakpoint, disposition=disp_donttouch, thread=-1, task=0, ignore_count=0, ops=0xb92300 <bkpt_breakpoint_ops>, from_tty=1, enabled=1, flags=0, display_canonical=0, internal=0, sals=...)
    at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/breakpoint.c:9274
#16 0x00000000004bfe1f in create_breakpoint_sal (display_canonical=0, flags=0, internal=0, enabled=1, from_tty=1, ops=0xb92300 <bkpt_breakpoint_ops>, ignore_count=0, task=0, thread=-1, 
    disposition=disp_donttouch, type=bp_breakpoint, extra_string=0x0, cond_string=0x0, filter=0x0, location=0x1a1cb10, gdbarch=0x1547600, sals=...)
    at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/breakpoint.c:9410
#17 create_breakpoints_sal (gdbarch=0x1547600, canonical=0x7ffcfaae4f00, cond_string=0x0, extra_string=0x0, type=bp_breakpoint, disposition=disp_donttouch, thread=-1, task=0, ignore_count=0, 
    ops=0xb92300 <bkpt_breakpoint_ops>, from_tty=1, enabled=1, internal=0, flags=0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/breakpoint.c:9464
#18 0x00000000004c058a in create_breakpoint (gdbarch=0x1547600, location=location@entry=0x1a1cc50, cond_string=cond_string@entry=0x0, thread=-1, thread@entry=0, extra_string=0x0, 
    extra_string@entry=0x1a0ac4e "", parse_extra=parse_extra@entry=1, tempflag=0, type_wanted=bp_breakpoint, ignore_count=0, pending_break_support=AUTO_BOOLEAN_AUTO, ops=0xb92300 <bkpt_breakpoint_ops>, 
    from_tty=1, enabled=1, internal=0, flags=0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/breakpoint.c:9886
#19 0x00000000004c0a29 in break_command_1 (arg=0x1a0ac4e "", flag=<optimised out>, from_tty=1) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/breakpoint.c:9994
#20 0x0000000000440ae3 in cmd_func (cmd=0x14072a0, args=0x1a0ac42 "start_kernel", from_tty=1) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/cli/cli-decode.c:1913
#21 0x00000000005f3d20 in execute_command (p=<optimised out>, p@entry=0x1a0ac40 "b start_kernel", from_tty=1) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/top.c:674
#22 0x00000000005270bc in command_handler (command=0x1a0ac40 "b start_kernel") at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:590
#23 0x00000000005273d8 in command_line_handler (rl=<optimised out>) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:780
#24 0x00000000005266cc in gdb_rl_callback_handler (rl=0x1a01ac0 "") at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:213
#25 0x0000000000636d8f in rl_callback_read_char () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/readline/callback.c:220
#26 0x00000000005265fe in gdb_rl_callback_read_char_wrapper_noexcept () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:175
#27 0x0000000000526679 in gdb_rl_callback_read_char_wrapper (client_data=<optimised out>) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:192
#28 0x0000000000526bf0 in stdin_event_handler (error=<optimised out>, client_data=0x13b40a0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-top.c:518
#29 0x0000000000525a05 in gdb_wait_for_event (block=block@entry=1) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-loop.c:859
#30 0x0000000000525b52 in gdb_do_one_event () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-loop.c:347
#31 0x0000000000525cae in gdb_do_one_event () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-loop.c:304
#32 start_event_loop () at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/event-loop.c:371
#33 0x00000000005766e8 in captured_command_loop (data=data@entry=0x0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/main.c:325
#34 0x0000000000527ce3 in catch_errors (func=func@entry=0x5766c0 <captured_command_loop(void*)>, func_args=func_args@entry=0x0, errstring=errstring@entry=0x7a29b4 "", mask=mask@entry=RETURN_MASK_ALL)
    at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/exceptions.c:236
#35 0x000000000057754e in captured_main (data=0x7ffcfaae52e0) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/main.c:1148
#36 gdb_main (args=args@entry=0x7ffcfaae5410) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/main.c:1158
#37 0x00000000004082d5 in main (argc=<optimised out>, argv=<optimised out>) at /work/risc-v/riscv-gnu-toolchain/riscv-binutils-gdb/gdb/gdb.c:32
```

A temporary fix for the "Register 834 is not available" error ( https://github.com/riscv/riscv-gnu-toolchain/issues/217 )

```
static int
riscv_breakpoint_kind_from_pc (struct gdbarch *gdbarch, CORE_ADDR *pcptr)
{
  //
  // a temporary fix to allow GDB to work with QEMU gdbstub
  //
  gdbarch_tdep (gdbarch)->supports_compressed_isa = SUP_NO;
  return 4;

......
}
```
