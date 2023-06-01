## 龙芯 2K0500

[For English](https://github.com/LA-Xenomai/doc/blob/master/doc/loongson-2k0500-en.md)

### 启动过程

2K0500开发板中与启动过程相关的资源包括：

- U-Boot：位于 SPI flash 中，作为固件使用
- Linux 镜像：位于 nand flash 中，需要使用 U-Boot 工具 `mkimage` 构建为 uImage
- 根文件系统镜像：位于 nand flash 中，使用 buildroot 制作

启动过程为：

1. 上电后，首先运行 SPI flash 中的 U-Boot
2. U-Boot 从 nand flash 中寻找并加载 Linux 内核镜像 uImage
3. Linux 内核初始化结束时从 nand flash 挂载根文件系统

### uImage 构建

![](../img/mk-uimage.png)

Linux 内核可参考以下脚本构建 uImage：
```sh
#!/usr/bin/bash
cd $(LINUX_ROOT)
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
1. 进入 Linux 源码目录 `$(LINUX_ROOT)`
2. 生成默认的内核编译配置文件
3. 编译内核，生成二进制文件 `vmlinux.bin`
4. 使用 `gzip` 将 `vmlinux.bin` 压缩为 `vmlinux.bin.gz`
5. 运行脚本 `mkuboot.sh`，该脚本调用工具 `mkimage`，该工具源自 U-Boot 项目。
    - `-a` 选项指定内核加载地址，如 `0x9000000000200000`
    - `-e` 选项指定内核入口地址，如 `0x90000000008973ec`
    - `-n` 选项指定内核名称，如 `linux-4.19.190-xeno`

> 注：
>   1. 非 Linux 内核可参考以上方法构建 uImage
>   2. 在 Linux 源码目录中完成配置后，可使用 `make uImage` 直接生成 uImage
