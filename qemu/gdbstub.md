

GDB packet processing

```
#0  gdb_handle_packet (s=0x5604b7b0d040, line_buf=0x5604b7b0d05c "g") at /work/risc-v/riscv-qemu/gdbstub.c:788
#1  0x00005604b66ed0cd in gdb_read_byte (s=0x5604b7b0d040, ch=55) at /work/risc-v/riscv-qemu/gdbstub.c:1460
#2  0x00005604b66ed1ed in gdb_chr_receive (opaque=0x0, 
    buf=0x7ffdcb5ff120 "+$g#67;c#a8028dfc0,40#5cs+;swbreak+;hwbreak+;qRelocInsn+;fork-events+;vfork-events+;exec-events+;vContSupported+;QThreadEvents+;no-resumed+#df", size=6)
    at /work/risc-v/riscv-qemu/gdbstub.c:1668
#3  0x00005604b675f20b in qemu_chr_be_write_impl (s=0x5604b7b0c330, 
    buf=0x7ffdcb5ff120 "+$g#67;c#a8028dfc0,40#5cs+;swbreak+;hwbreak+;qRelocInsn+;fork-events+;vfork-events+;exec-events+;vContSupported+;QThreadEvents+;no-resumed+#df", len=6) at qemu-char.c:388
#4  0x00005604b675f269 in qemu_chr_be_write (s=0x5604b7b0c330, 
    buf=0x7ffdcb5ff120 "+$g#67;c#a8028dfc0,40#5cs+;swbreak+;hwbreak+;qRelocInsn+;fork-events+;vfork-events+;exec-events+;vContSupported+;QThreadEvents+;no-resumed+#df", len=6) at qemu-char.c:400
#5  0x00005604b6763691 in tcp_chr_read (chan=0x5604b7a228a0, cond=G_IO_IN, opaque=0x5604b7b0c330) at qemu-char.c:2966
#6  0x00005604b68e71b2 in qio_channel_fd_source_dispatch (source=0x5604b7b0cfc0, callback=0x5604b676353b <tcp_chr_read>, user_data=0x5604b7b0c330) at io/channel-watch.c:84
#7  0x00007f94a1bbc04a in g_main_context_dispatch () from /lib/x86_64-linux-gnu/libglib-2.0.so.0
#8  0x00005604b6865f2e in glib_pollfds_poll () at main-loop.c:213
#9  0x00005604b6866009 in os_host_main_loop_wait (timeout=0) at main-loop.c:258
#10 0x00005604b68660b6 in main_loop_wait (nonblocking=1) at main-loop.c:506
#11 0x00005604b676cb9b in main_loop () at vl.c:1947
#12 0x00005604b6774331 in main (argc=10, argv=0x7ffdcb600678, envp=0x7ffdcb6006d0) at vl.c:4662
```
