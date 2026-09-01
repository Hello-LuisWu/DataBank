# gcc install

## Linux

```sh
# Debian/Ubuntu
sudo apt update
sudo apt install build-essential

# Archlinux
sudo pacman -S base-devel

# Rocky Linux
sudo dnf group install "Development Tools"
```

## termux

```sh
pkg install -y gcc g++ make cmake ninja gdb valgrind git \
libstdc++ libstdc++-dev libandroid-support-dev pkg-config \
openssl-dev zlib-dev bzip2-dev readline-dev ncurses-dev
```

| 软件包                      | 用途               |
|-----------------------------|--------------------|
| `gcc/g++`                   | c/c++ 编译器       |
| `gdb`                       | 调试器             |
| `make/cmake`                | 构建工具           |
| `cmake / ninja`             | 现代跨平台构建系统 |
| `pkg-config`                | 库编译配置工具     |
| `autoconf/automake/libtool` | 辅助工具           |
| `libstdc++‑dev`             | c++ 标准库头文件   |

