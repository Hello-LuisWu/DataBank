# termux 从零开始配置

## 0 下载 termux 

下载 termux APK 文件：使用浏览器访问 Termux 的官方 GitHub 发布页： [termux](https://github.com/termux/termux-app/releases)

在 “Assets” 列表中，找到结尾有 `arm64` 字样的第一条链接，点击下载

![](files/Pasted%20image%2020260816184104.png)

## 1 第一次启动 termux

termux 默认 家目录位置： `/data/data/com.termux/files/home` ， 可用 `echo $HOME` 命令查看 

### 1.1 更新软件源

第一次必须更新！确保拥有最新的软件列表和包管理工具

```sh
pkg update && pkg upgrade -y
```

命令解释：

- pkg update：更新软件源列表
- pkg upgrade：升级所有软件
- `-y` ：自动确认所有提示，让过程更顺畅。

### 1.2 pkg 基础常用命令

| 命令                   | 功能          | 说明                           |
| -------------------- | ----------- | ---------------------------- |
| `pkg update`         | **更新软件源**   | 获取最新的软件包列表，**安装任何软件前的第一步**   |
| `pkg upgrade`        | **升级所有软件**  | 将所有已安装的软件包升级到最新版本            |
| `pkg install <包名>`   | **安装软件包**   | 安装指定的软件包，可以一次性安装多个，用空格隔开     |
| `pkg uninstall <包名>` | **卸载软件包**   | 卸载指定的软件包，会保留修改过的配置文件         |
| `pkg search <关键词>`   | **搜索软件包**   | 在软件源中搜索与你输入的关键词相关的包。         |
| `pkg show <包名>`      | **查看包信息**   | 显示某个软件包的详细信息，如版本、描述等。        |
| `pkg list-installed` | **列出已安装包**  | 查看当前 Termux 中所有已安装的软件包       |
| `pkg list-all`       | **列出所有可用包** | 查看软件源中所有可供安装的软件包             |
| `pkg reinstall <包名>` | **重新安装包**   | 重新安装指定的软件包，可用于修复文件损坏等问题      |
| `pkg files <包名>`     | **列出包的文件**  | 列出指定软件包安装的所有文件及其路径           |
| `pkg autoclean`      | **清理缓存**    | 移除缓存中过时的 `.deb` 安装包文件        |
| `pkg clean`          | **彻底清理缓存**  | 移除缓存中**所有**已下载的 `.deb` 安装包文件 |

## 2 更换 termux 软件源

换源本质上是修改软件包下载地址。因为默认源在国外，国内访问不仅速度慢，还容易中断。换成国内镜像源后，下载速度能从几十 KB/s 提升到 MB/s 级别。

### 2.1 图形界面（TUI）替换

termux 有官方替换镜像源工具。通过图形界面引导操作完成换源：

```sh
termux-change-repo
```

若没有 `termux-change-repo` 命令，请安装相关工具：

```sh
pkg install termux-tools
```

再用 `termux-change-repo` 查看可用的源，出现的界面按 回车 确定；会出现以下结果：

```sh
[ ] xxxxxxxxxxxxx
[*] xxxxx Chinese xxxx
[ ] xxxxxxxxxxxx
```

用 ↑ ↓ 方向键选中 `Mirrors in Chinese Mainland` 中国大陆镜像组，或选择 `Tsinghua`（清华）、`USTC`（中科大）等镜像源。 按 空格 选择需要的源。再按 `enter` 确定选择

运行 `pkg update` 以确保更新生效。

### 2.2 命令替换

使用如下命令替换官方源为镜像源:

```sh
sed -i 's@^\(deb.*stable main\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/apt/termux-main stable main@' $PREFIX/etc/apt/sources.list
```

### 2.3 手动编辑配置文件

以上两个方式本质就是修改 `$PREFIX/etc/apt/sources.list` 文件，可以直接修改文件

这里将主源和副源都设置为[清华镜像源](https://mirrors.tuna.tsinghua.edu.cn/help/termux/)

用 nano 文本编辑器打开源配置文件：

```sh
nano $PREFIX/etc/apt/sources.list
```

将文件里的内容全部替换为以下内容：

```sh
# The termux repository mirror
deb https://mirrors.tuna.tsinghua.edu.cn/termux/apt/termux-main stable main
```

 **保存并退出**：按 `Ctrl + X`，然后按 `Y`，最后按回车确认保存。

运行 `pkg update` 以确保更新生效。

### 2.4 副源

前面所接受的是主源的配置方法。下面是副源的配置方法

何为主源、副源？简单来说，主源-是包含常规软件的核心仓库 ， 副源 （官方称为社区源）-则是为满足特定需求而设立的可选扩展仓库。

默认**未启用**，需手动安装对应 `-repo` 包来激活：

| 仓库名称     | 启用命令                                                                                                              | 用途                                                                                                                                                                            | 配置文件位置                                                                               |
| -------- | ----------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| **Root** | `pkg install root-repo`[](https://wiki.termux.com/index.php?title=Package_Management&direction=prev&oldid=6475#1) | 包含需要 **Root 权限**才能运行的软件包[](https://app4.secure.forcepoint.com/termux/termux-packages/wiki/Creating-new-package/0fcacc832d747362b58522fe0ea0440b55acec37#1)。                   | `$PREFIX/etc/apt/sources.list.d/root.list`[](https://mirror.lzu.edu.cn/help/termux/) |
| **X11**  | `pkg install x11-repo`[](https://wiki.termux.com/index.php?title=Package_Management&direction=prev&oldid=6475#1)  | 包含与**图形界面（X11）** 相关的软件包，如在 Termux 中运行图形化应用[](https://app4.secure.forcepoint.com/termux/termux-packages/wiki/Creating-new-package/0fcacc832d747362b58522fe0ea0440b55acec37#1)。 | `$PREFIX/etc/apt/sources.list.d/x11.list`[](https://mirror.lzu.edu.cn/help/termux/)  |

> **请注意**：`science-repo`（科学计算）和 `game-repo` 的内容已合并到主源中，不再需要单独启用。

## 3 安装基础工具

```sh
pkg install \
git \
wget \
neovim \
zip \
eza \
fzf \
ripgrep \
```

工具作用：

- git：从 GitHub 和 gitee 拉取代码
- wget：文件下载
- vim：文本编辑器
- zip：压缩工具
- eza（可选）：文件查看工具；替代原生的 `ls` 命令
- fzf（可选）：文件搜索工具
- ripgrep（可选）：文件内容查找工具

## 4 开启手机存储访问权限

Termux 默认不能访问手机文件。

```sh
termux-setup-storage
```

弹窗点击 `允许`

可通过 `cd /storage/emulated/0/` 命令访问手机存储空间。

家目录会生成 `storage` 目录，目录内有链接手机存储空间的目录，用 `ls -l ~/storage` 命令查看：

```
total 0
lrwxrwxrwx. 1 u0_a345 u0_a345 34 Aug 16 16:11 dcim -> /storage/emulated/0/DCIM
lrwxrwxrwx. 1 u0_a345 u0_a345 34 Aug 16 16:11 downloads -> /storage/emulated/0/Download
lrwxrwxrwx. 1 u0_a345 u0_a345 34 Aug 16 16:11 movies -> /storage/emulated/0/Movies
lrwxrwxrwx. 1 u0_a345 u0_a345 34 Aug 16 16:11 music -> /storage/emulated/0/Music
lrwxrwxrwx. 1 u0_a345 u0_a345 34 Aug 16 16:11 pictures -> /storage/emulated/0/Pictures
lrwxrwxrwx. 1 u0_a345 u0_a345 34 Aug 16 16:11 shared -> /storage/emulated/0
```

链接都指向 `/storage/emulated/0` 目录

## 5 开启 ssh

### 5.1安装 ssh：

```sh
pkg instsll openssh
```

### 5.2打开 termux 自动启动 ssh 

以下命令逐一执行

```sh
pkg install termux-services # 安装官方服务管理器

exit            # 重新进入 termux

sv up sshd      # 立刻手动开启ssh

sv-enable sshd  # 自动启动 ssh

sv-disable sshd # 关闭 ssh 自启
```

服务命令汇总：

```
sv up sshd      # 立刻手动开启ssh
sv down sshd    # 关闭ssh 
sv status sshd  # 查看是否正在运行
sv-enable sshd  # 自动启动 ssh
sv-disable sshd # 关闭 ssh 自启
```

### 5.4 查看用户名

```sh
whoami

# 例如输出用户名：
u0_a123
```

如果需要更改用户名。请使用 

### 5.3 设置账户密码：

ssh 连接必须设置账户登录密码！termux 默认没有登录密码，可用 `passwd` 命令设置账户密码

```sh
passwd
```

此时出现输入密码提示：

1. 第一遍输入要设置的密码，按回车确认
2. 第二遍确认密码，按回车确认

### 5.5 查看手机 ip 

```sh
ifconfig
```



### 5.6 ssh 连接

termux ssh 默认端口不是 `22` 而是`8022`

```sh
# 用端口参数 -p 指定 8022 端口
ssh -p 8022 用户名@手机ip

# 例如：
ssh -p 8022 u0_123@192.168.1.35
```

其中 `u0_123` 是用 `whoami` 命令查看的用户名。 `192.168.1.35` 使用 `ifconfig` 命令查看的手机 ip 地址 

### 5.7 常用 SSH 管理命令汇总

```sh
sv up sshd      # 立刻手动开启ssh
sv down sshd    # 关闭ssh 
sv status sshd  # 查看是否正在运行
sv-enable sshd  # 自动启动 ssh
sv-disable sshd # 关闭 ssh 自启
whoami          # 查看用户名 
ifconfig        # 查看本机IP
```

## 安装实用工具

全部就绪，开始安装各种工具。以下是一些常用的包和对应的安装命令

| 工具/用途             | 安装命令                 | 说明               |
| ----------------- | -------------------- | ---------------- |
| **Python**        | `pkg install python` | 安装 Python 3 编程环境 |
| **Node.js**       | `pkg install nodejs` | 安装 Node.js 运行环境  |
| **Clang (C/C++)** | `pkg install clang`  | C/C++ 编译器        |
|                   |                      |                  |
