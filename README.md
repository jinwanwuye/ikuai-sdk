# iKuai SDK 使用指南

## 1. 简介
本文档介绍如何使用 iKuai SDK 进行交叉编译，并将编译的程序部署到 iKuai 设备上运行。

## 2. 环境准备

### 2.1 下载并解压 SDK
```bash
# 下载 SDK（替换为实际的下载链接）
wget https://github.com/deadash/ikuai-sdk/releases/download/v0.0.1/x86_64-buildroot-linux-uclibc_sdk-buildroot.tar.gz


### 2.2 创建 SDK 目录
mkdir -p /opt/sdk/ikuai
cd /opt/sdk/ikuai
```

### 解压 SDK
```bash
tar xf x86_64-buildroot-linux-uclibc_sdk-buildroot.tar.gz --strip-components=1 -C /opt/sdk/ikuai
```
### 执行 relocate 脚本
```bash
cd /opt/sdk/ikuai
./relocate-sdk.sh /opt/sdk/ikuai
```


### 2.3 配置环境变量
```bash
# 创建环境设置文件
cat > /opt/sdk/ikuai/environment-setup << EOF
export SDKTARGETSYSROOT="/opt/sdk/ikuai/sysroot"
export PATH="/opt/sdk/ikuai/bin:$PATH"
export CC="$(basename $(find /opt/sdk/ikuai/bin -name '*-gcc'))"
export CXX="$(basename $(find /opt/sdk/ikuai/bin -name '*-g++'))"
export CROSS_COMPILE="$(echo $CC | sed 's/-gcc$//')-"
export PKG_CONFIG_PATH="$SDKTARGETSYSROOT/usr/lib/pkgconfig"
export PKG_CONFIG_SYSROOT_DIR="$SDKTARGETSYSROOT"
EOF

# 激活环境变量
source /opt/sdk/ikuai/environment-setup
```


## 3. 编译测试程序

### 3.1 C 程序测试
```bash
# 创建测试目录
mkdir test_c && cd test_c

# 创建测试源文件
cat > hello.c << EOF
#include <stdio.h>

int main() {
    printf("Hello from iKuai!\n");
    return 0;
}
EOF

# 编译
$CC hello.c -o hello
```


### 3.2 C++ 程序测试
```bash
# 创建测试目录
mkdir test_cpp && cd test_cpp

# 创建测试源文件
cat > hello.cpp << EOF
#include <iostream>

int main() {
    std::cout << "Hello from iKuai!" << std::endl;
    return 0;
}
EOF

# 编译
$CXX hello.cpp -o hello
```


## 4. 部署到 iKuai 设备

### 4.1 检查编译结果
```bash
# 验证可执行文件
file hello
ldd hello
```


### 4.2 上传到 iKuai 设备
```bash
# 使用 scp 上传（替换为实际的 IP 地址）
scp hello root@<IKUAI_IP>:/root/
```


### 4.3 在 iKuai 设备上执行
```bash
# SSH 登录到设备
ssh root@<IKUAI_IP>

# 添加执行权限
chmod +x /root/hello

# 运行程序
./hello
```
