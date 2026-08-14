# Node.js / npm 安装与配置指南

## 使用 NVM 安装（强烈推荐，全发行版通用）

使用 NVM（Node Version Manager）可以管理多个 Node 版本。

在实际开发中，不同的项目可能需要不同版本的 Node。nvm 可以让你在同一台电脑上安装并随时切换多个 node 版本。

优点：

- 可以安装多个 Node 版本
- 可以快速切换 Node 版本
- 不污染系统环境
- 适合开发环境

### Linux/Unix（macOS）安装方法

安装脚本：

```sh
# curl
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.4/install.sh | bash

# 或 wget
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.4/install.sh | bash
```

加载环境变量：

```sh
# bash
source ~/.bashrc

# 或 zsh
source ~/.zshrc
```

### Windows 安装方法

**下载：**

在 [nvm-windows](https://github.com/coreybutler/nvm-windows/releases) 下载页面下载 `nvm-setup.exe` 安装文件并安装

**验证：**

重启终端 执行 `nvm -v` 验证是否安装成功。

**设置环境变量：**

一般不需要，如果找不到 nvm 命令可以设置

nvm 安装位置为 `C:\Users\ll\AppData\Local\nvm` 将这个地址复制一下

按一下 开始菜单按钮，搜索 编辑系统环境变量-环境变量-新建系统变量，变量名：NVM_HOME, 变量值为刚刚复制 nvm 的安装路径。将复制的地址添加即可

Windows 的 nvm 每次启动终端都会保持当前版本，不需要额外配置 `nvm alias default`

### nvm 基础命令

```sh
# 安装 nodejs 20
nvm install 20

# 安装 nodejs 25
nvm install 25

# 安装最新的 lts 长期支持版
nvm install --lts

# 切换到 nodejs 20 版本
nvm use 20

# 切换到系统自带的 nodejs 版本
nvm use system

# 切换到 LTS 长期支持版
nvm use --lts

# 查看已经安装过的 nodejs 版本
nvm ls
# 或
nvm list

# 查看远程可安装的 nodejs 版本
npm ls-remote

# 如果想快速知道可用版本
nvm ls-remote | grep -E "^v25"

# 设置默认版本（打开终端默认打开的版本）
nvm alias default 20

# 查看默认版本
nvm alias

# 卸载安装过的版本
nvm uninstall 20
```

## 使用系统包管理器安装 Node

如果不想使用 nvm，也可以直接使用系统包管理器安装。

但缺点是 不方便切换版本。

### MacOS

```shell
brew install node
```

---

### Ubuntu/Debian

```shell
# 使用官方仓库（简单但版本可能较旧）:
sudo apt update
sudo apt install nodejs npm

# 或

# 使用 NodeSource PPA（推荐，可安装指定版本）:
# 添加指定版本的源
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -

# 更新源
sudo apt update

# 安装 nodejs
sudo apt install -y nodejs
```

---

### Arch（Manjaro）

```sh
# Arch 始终提供非常新（通常是最新稳定版）的 Node.js。
sudo pacman -Sy nodejs npm

# 如果你需要 LTS 版本
sudo pacman -S nodejs-lts-iron  # "iron" 是当前 20.x LTS 的代号

# nodejs-lts-hydrogen     Node 18
# nodejs-lts-iron         Node 20

# 查看可用版本
pacman -Ss nodejs-lts
```

---

### CentOS/RHEL/Fedora

```sh
# 查看可用的 nodejs 模块
sudo dnf module list nodejs

# 启用某个版本（例如 20）
sudo dnf module enable nodejs:20

# 安装
sudo dnf install nodejs
```

### 老版本CentOS(7)

通常需要通过 NodeSource

```
curl -fsSL https://rpm.nodesource.com/setup_20.x | sudo bash -

sudo yum install -y nodejs
```

---

### Windows

在 [nodejs下载页面](https://nodejs.org/zh-cn/download)，下载 适用于 Windows 的 msi 文件并安装

当你执行 npm 命令时，PowerShell 尝试加载 npm.ps1 脚本，但被系统安全策略拦截了。

在 PowerShell 运行以下命令，将执行策略更改为 RemoteSigned（允许运行本地创建的脚本，但从互联网下载的脚本必须经过签名）。

```sh
set-ExecutionPolicy RemoteSigned
```


## npm 换源配置

npm 默认使用官方源：`https://registry.npmjs.org`, 国内访问可能较慢，可以使用镜像源。

### 临时换源：

```sh
npm install xxx --registry=https://registry.npmmirror.com
```

临时换源使用了 `--registry` 这个参数。以上命令中，`xxx` 为安装的包名。仅此次安装临时使用了 https://registry.npmmirror.com 源，但安装结束后 npm 源不会改变。应用场景：速度慢临时换源，某些包特定源才有，CI/脚本指定源

### 永久换源：

```sh
npm config set registry https://registry.npmmirror.com
```

以上换的源会写入 `~/.npmrc` 配置文件里永久生效

**查看当前源：**

```sh
npm config get registry
```

输出:

```sh
https://registry.npmmirror.com
```

### 更改 npm 配置文件换源

npm 的永久配置写在 `~/.npmrc` 文件里。打开 `.npmrc` 文件更改 registry 项

```sh
registry=https://registry.npmmirror.com
```

### 项目级源

只影响当前项目，不影响其他项目。项目配置：`项目目录/.npmrc`。在项目目录创建 `.npnrc` 文件，写入：

```sh
registry=https://registry.npmmirror.com
```

### 安装 nrm 源管理工具

Nodejs nrm(npm registry manager) 是一个管理 npm 源的工具。可以快速切换多个镜像源。

```sh
npm install -g nrm
```

通过 `nrp -v` 查看是否安装

#### 列出已有的源

```sh
nrm ls
```

输出以下结果:

```sh
  npm ---------- https://registry.npmjs.org/
  yarn --------- https://registry.yarnpkg.com/
  tencent ------ https://mirrors.cloud.tencent.com/npm/
  cnpm --------- https://r.cnpmjs.org/
* taobao ------- https://registry.npmmirror.com/
  npmMirror ---- https://skimdb.npmjs.com/registry/
```
以上输出结果中, 第一列为镜像源的名称, 最后面的是镜像源的 URL 地址, 开头带 `*` 的是正在使用的源.

#### 查看当前使用的源名称

```sh
nrm current
```

输出以下结果:

```sh
You are using taobao registry.
```

以上输出结果告诉我们正在使用的是淘宝的镜像源

#### 查看当前使用源的 URL

```sh
npm config get registry
```

输出以下结果:

```sh
https://registry.npmmirror.com/
```

以上输出结果告诉我们正在使用的是淘宝的URL镜像源

#### 添加镜像源

```sh
nrm add [镜像源名称] [URL]
```

例如添加淘宝源:

```sh
nrm add taobao https://registry.npmmirror.com/
```

注: 淘宝之前的镜像源 <http://npm.taobao.org> 和 <http://registry.npm.taobao.org> 域名已于 2022 年 05 月 31 日零时起停止服务, 新镜像源地址:
- web 站点: https://npmmirror.com/
- registry 站点: https://registry.npmmirror.com/
- 二进制文件镜像: https://npmmirror.com/mirrors

#### 切换镜像源

以淘宝为例

```sh
nrm use taobao
```

#### 测试镜像源速度

以淘宝为例

```sh
nrm test taobao
```

输出以下结果:

```sh
* taobao ---- 144 ms
```

测试所有添加源的速度：

```sh
nrm test
```

输出以下结果：

```sh
  npm ---------- 1131 ms
  yarn --------- 1111 ms
  tencent ------ 619 ms
  cnpm --------- 1113 ms
* taobao ------- 271 ms
  npmMirror ---- 2705 ms (Fetch error, if this is your private registry, please ignore)
  huawei ------- 512 ms
```

#### 删除镜像源, 以淘宝为例

```sh
nrm del taobao
```

## yarn 安装及配置

yarn 是 JavaScript 包管理器，和npm类似，用来替代早期比较慢的 npm。若无项目、软件或插件需要不推荐使用！因为 npm 这几年已经变快了！

安装 yarn:

```sh
npm install -g yarn
```

换源:

```sh
yarn config set registry https://registry.npmmirror.com
```

解决node-sass无法下载下来的问题:

```sh
yarn config set sass_binary_site https://cdn.npmmirror.com/dist/node-sass
```

查看yarn镜像设置：

```sh
yarn config get registry
```
