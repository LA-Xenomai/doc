## Loongson 3A5000 + 7A2000

### Booting Process

Resources related to booting process:

- PMON/UEFI Firmware: Located in ROM, "old world" firmware in LoongArch software environment
- Linux kernel: Located in `/boot` directory of disk partition, (compressed) ELF file, namely vmlinux or vmlinuz
- rootfs: Located in disk partition, based on distribution [Loongnix](http://www.loongnix.cn/zh/loongnix/)

Booting process:

1. Running firmware from ROM
2. Firmware loads kernel or second level bootloader(SLB):
    - PMON: load kernel in `/boot`
    - UEFI firmware: load SLB grub, then load kernel with grub
3. Linux kernel mounts rootfs from specified disk partition
