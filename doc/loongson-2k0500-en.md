## Loongson 2K0500

### Booting Process

Resources related to booting process:

- U-Boot: Located in SPI flash, works as firmware
- Linux Image: Located in nand flash, generated as uImage with U-Boot tool `mkimage`
- Rootfs Image：Located in nand flash, generated with buildroot

Booting process:

1. Running U-Boot from SPI flash
2. U-Boot loads uImage from nand flash
3. Linux kernel mounts rootfs from nand flash

### How to Build uImage

![](./img/mk-uimage.png)

According to the following script to build uImage from Linux source code:
```sh
#!/usr/bin/bash
cd $(LINUX_SRC_HOME)
make *defconfig
make vmlinux.bin -j
cat arch/loongarch/boot/vmlinux.bin | \
    gzip -n -f -9 > arch/loongarch/boot/vmlinux.bin.gz
bash scripts/mkuboot.sh \
    -A loongarch \
    -O linux \
    -C gzip  \
    -T kernel \
    -a $(VMLINUX_LOAD_ADDRESS) \
    -e $(VMLINUX_ENTRY_POINT) \
    -n $(KERNEL_NAME) \
    -d arch/loongarch/boot/vmlinux.bin.gz \
    uImage
```
1. Enter Linux source home `$(LINUX_SRC_HOME)`
2. Generate default configuration
3. Compile kernel, generate binary file `vmlinux.bin`
4. Compress `vmlinux.bin` to `vmlinux.bin.gz` with `gzip` 
5. Run script `mkuboot.sh`, which calls `mkimage`
    - `-a` set load address, such as `0x9000000000200000`
    - `-e` set entry point, such as `0x90000000008973ec`
    - `-n` set image name, such as `linux-4.19.190-xeno`

> Notes:
>   1. Non-Linux kernel can refer to the above method to build uImage
>   2. After Linux pre-compile configuration, you can use `make uImage` command to generate uImage directly
