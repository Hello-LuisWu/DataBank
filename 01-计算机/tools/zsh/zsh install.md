# 安装 zsh

### macOS 安装 zsh:

```sh
brew install zsh
```

### Ubuntu 安装 zsh:

```sh
sudo apt install -y zsh
```

### Arch (manjaro) 安装 zsh:

```sh
sudo pacman -Sy zsh
```

### Rocky linux:

```sh
dnf -y install zsh
```

---

# zsh 设置

### 查看系统已有的 shell:

```sh
cat /etc/shells
```

**输出结果：**

```sh
/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/usr/bin/sh
/bin/dash
/usr/bin/dash
/bin/zsh
/usr/bin/zsh
```
以上输出结果可以看到已经有 zsh 了

### 切换默认 shell:

```sh
chsh -s $(which zsh)
```

**注:** rockylinux 可能找不到 chsh 命令：

```sh
安装：
sudo dnf install -y util-linux-user

# 查看 zsh 路径
which zsh

然后：
chsh -s /usr/bin/zsh

如果是 root：
chsh -s /usr/bin/zsh root
```

rockylinux 还可以：
```sh
# root 是你当前的用户名
usermod -s /bin/zsh root
```

重启系统！

### 查看当前使用的 shell:

```sh
echo $SHELL
```

**输出结果：**

```sh
/bin/zsh
```

以上输出结果可以看到，默认的 shell 已经变成了 zsh 
