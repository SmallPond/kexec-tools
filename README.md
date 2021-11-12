# 简介

源版本 `main 61b8c79b0fb76490f944fd5e61f25edd81f2a46`

kexec for raspberrypi 4 kernel 5.10.20

# How to Run

```c
./bootstrap
LDFLAGS=-static ./configure --host=aarch64-linux-gnu  --without-zlib --without-lzm
make 
```

替换生成的kexec 可执行文件

```
cp build/sbin/kexec /sbin/kexec
```


# 其他

- 调试
```
int kexec_debug = 1; // 开启调试  kexe.c 文件中
```

- 加载命令
```
kexec -p --initrd=/boot/legacy-image-arm64.zbi /boot/rpi4-boot-shim.elf
```

# 4 种类镜像格式

定义在 `kexec/arch/arm64/kexec-arm64.c` 文件中
```c
struct file_type file_type[] = {
	{"vmlinux", elf_arm64_probe, elf_arm64_load, elf_arm64_usage},
	{"Image", image_arm64_probe, image_arm64_load, image_arm64_usage},
	{"uImage", uImage_arm64_probe, uImage_arm64_load, uImage_arm64_usage},
	{"zImage", zImage_arm64_probe, zImage_arm64_load, zImage_arm64_usage},
};

```

## 如何判断镜像为 Image 格式


```
static const uint8_t arm64_image_magic[4] = {'A', 'R', 'M', 0x64U};
static const uint8_t arm64_image_pe_sig[2] = {'M', 'Z'};
```

