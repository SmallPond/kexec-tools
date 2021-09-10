
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

# 源版本

main 61b8c79b0fb76490f944fd5e61f25edd81f2a46


