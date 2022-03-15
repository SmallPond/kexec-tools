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

```bash
cp build/sbin/kexec /sbin/kexec
# or
./install.sh
```


# 其他

- 调试
```c
int kexec_debug = 1; // 开启调试，定义在 kexe.c 文件中
```

- 加载命令

```bash
kexec -p  /boot/rpi4-boot.img --dtb=/boot/bcm2711-rpi-4-b.dtb

# 从 USB 固态硬盘中加载
kexec -p  /mnt/db/raspi4/rpi4-boot.img --dtb=/mnt/db/raspi4/bcm2711-rpi-4-b.dtb
```
- drop page cache `sync; echo 1 > /proc/sys/vm/drop_caches`

- kexec 用户态工具加载内核文件的时机：
	- `my_load()` -> `slurp_decompress_file()`
	 	
- 实际加载文件的系统调用 `(long) syscall(__NR_kexec_load, entry, nr_segments, segments, flags);`
	- 使用 ktrace 测试时间 `trace-cmd record -p function_graph -l do_kexec_load -l __arm64_sys_kexec_load`  [14-17 ms]
	- 这里应该是 copy_from_user 的时间 


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


```c
static const uint8_t arm64_image_magic[4] = {'A', 'R', 'M', 0x64U};

static const uint8_t arm64_image_pe_sig[2] = {'M', 'Z'};
```

# 测试硬盘读写速度

```bash
# 写速度
time dd if=/dev/zero of=test.file bs=512M count=2 oflag=direc
# 读速度
hdparm -Tt /dev/mmcblk0p2
```
