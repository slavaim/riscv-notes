
Create the rootfs directory structures

```
make install
```

Create a console device

```
$ cd dev
$ sudo mknod -m 622 console c 5 1
```

The initramfs directories tree

```
├── bin
│   ├── ash -> busybox
│   ├── bash -> busybox
│   ├── busybox
│   ├── cat -> busybox
│   ├── chgrp -> busybox
│   ├── chmod -> busybox
│   ├── chown -> busybox
│   ├── cp -> busybox
│   ├── date -> busybox
│   ├── dd -> busybox
│   ├── df -> busybox
│   ├── dmesg -> busybox
│   ├── echo -> busybox
│   ├── egrep -> busybox
│   ├── false -> busybox
│   ├── fgrep -> busybox
│   ├── fsync -> busybox
│   ├── grep -> busybox
│   ├── iostat -> busybox
│   ├── ln -> busybox
│   ├── ls -> busybox
│   ├── mkdir -> busybox
│   ├── mknod -> busybox
│   ├── mktemp -> busybox
│   ├── mpstat -> busybox
│   ├── mv -> busybox
│   ├── nice -> busybox
│   ├── pidof -> busybox
│   ├── printenv -> busybox
│   ├── ps -> busybox
│   ├── pwd -> busybox
│   ├── rm -> busybox
│   ├── rmdir -> busybox
│   ├── sh -> busybox
│   ├── sleep -> busybox
│   ├── stat -> busybox
│   ├── stty -> busybox
│   ├── sync -> busybox
│   ├── touch -> busybox
│   ├── true -> busybox
│   ├── umount -> busybox
│   ├── uname -> busybox
│   ├── usleep -> busybox
│   ├── vi -> busybox
│   └── watch -> busybox
├── dev
│   └── console
├── init
├── sbin
│   ├── blkid -> ../bin/busybox
│   ├── depmod -> ../bin/busybox
│   ├── fdisk -> ../bin/busybox
│   ├── findfs -> ../bin/busybox
│   ├── init -> ../bin/busybox
│   ├── insmod -> ../bin/busybox
│   ├── loadkmap -> ../bin/busybox
│   ├── lsmod -> ../bin/busybox
│   ├── modinfo -> ../bin/busybox
│   ├── modprobe -> ../bin/busybox
│   ├── rmmod -> ../bin/busybox
│   ├── setconsole -> ../bin/busybox
│   └── sysctl -> ../bin/busybox
└── usr
    ├── bin
    │   ├── awk -> ../../bin/busybox
    │   ├── chvt -> ../../bin/busybox
    │   ├── clear -> ../../bin/busybox
    │   ├── cmp -> ../../bin/busybox
    │   ├── cut -> ../../bin/busybox
    │   ├── diff -> ../../bin/busybox
    │   ├── dirname -> ../../bin/busybox
    │   ├── du -> ../../bin/busybox
    │   ├── env -> ../../bin/busybox
    │   ├── expand -> ../../bin/busybox
    │   ├── find -> ../../bin/busybox
    │   ├── flock -> ../../bin/busybox
    │   ├── head -> ../../bin/busybox
    │   ├── hostid -> ../../bin/busybox
    │   ├── lsof -> ../../bin/busybox
    │   ├── mkfifo -> ../../bin/busybox
    │   ├── nmeter -> ../../bin/busybox
    │   ├── patch -> ../../bin/busybox
    │   ├── pkill -> ../../bin/busybox
    │   ├── pmap -> ../../bin/busybox
    │   ├── printf -> ../../bin/busybox
    │   ├── pstree -> ../../bin/busybox
    │   ├── readlink -> ../../bin/busybox
    │   ├── realpath -> ../../bin/busybox
    │   ├── reset -> ../../bin/busybox
    │   ├── resize -> ../../bin/busybox
    │   ├── seq -> ../../bin/busybox
    │   ├── showkey -> ../../bin/busybox
    │   ├── smemcap -> ../../bin/busybox
    │   ├── sort -> ../../bin/busybox
    │   ├── split -> ../../bin/busybox
    │   ├── sum -> ../../bin/busybox
    │   ├── tail -> ../../bin/busybox
    │   ├── tee -> ../../bin/busybox
    │   ├── top -> ../../bin/busybox
    │   ├── tty -> ../../bin/busybox
    │   ├── unexpand -> ../../bin/busybox
    │   ├── uniq -> ../../bin/busybox
    │   ├── uptime -> ../../bin/busybox
    │   ├── wc -> ../../bin/busybox
    │   ├── which -> ../../bin/busybox
    │   ├── whoami -> ../../bin/busybox
    │   └── yes -> ../../bin/busybox
    └── sbin
        ├── chroot -> ../../bin/busybox
        ├── loadfont -> ../../bin/busybox
        └── setfont -> ../../bin/busybox
```

The init script

```
#!/bin/sh

echo -e "Welcome to \\e[1mRISC-V \\e[32mLinux \\e[31mLive\\e[0m (/init)"

# Wait 5 second or until any keybord key is pressed.
read -t 5 -n1 -s key

# Execute the shell
exec sh

# Create new mountpoint in RAM, make it our new root location and overlay it
# with our storage area (if overlay area exists at all). This operation invokes
# the script '/etc/03_init.sh' as the new init process.
#exec /etc/02_overlay.sh

echo "(/init) - you can never see this unless there is a serious bug..."

# Wait until any key has been pressed.
read -n1 -s
```
