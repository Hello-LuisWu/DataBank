# pip 详解

## 名词解释

### PyPA 制定规则的组织

PyPA（Python Packaging Authority） 是 Python 包装管理的官方组织，负责维护 pip、PyPI 这套打包分发的整套标准与工具。

### PyPI  存放 Python 包的仓库

**PyPI**（Python Package Index）是 Python 官方软件包仓库，[PyPI](https://pypi.org) 存放大量的 Python 包。

### pip 下载安装 Python 包的工具

`pip` 是 **Python 的包管理工具**（Package Installer for Python），用于安装、卸载、升级和管理 Python 第三方库。

| Python 生态 | 手机生态类比                                  | 作用                             |
| --------- | --------------------------------------- | ------------------------------ |
| **PyPA**  | **苹果 App Store / Google Play 的管理规则制定者** | 制定软件打包规范、维护工具生态，保证大家按照统一规则发布软件 |
| **PyPI**  | **App Store / Google Play 应用商店**        | 存放大量 Python 软件包，开发者把自己的库上传到这里  |
| **pip**   | **手机上的 App Store 客户端**                  | 用户通过它搜索、下载、安装、升级 Python 软件包    |


相当于 Python 世界里的 `apt`（Ubuntu）、`pacman`（Arch）、`brew`（macOS）等包管理器

## 1 语法

```sh
pip <command> [options] <package>
```

## 2  pip 常用命令

| 命令        | 作用         | 示例                       |
| --------- | ---------- | ------------------------ |
| install   | 安装包        | `pip install requests`   |
| uninstall | 卸载包        | `pip uninstall requests` |
| list      | 查看已安装包     | `pip list`               |
| show      | 查看包信息      | `pip show numpy`         |
| freeze    | 输出安装列表     | `pip freeze`             |
| search    | 搜索包（已弃用）   | -                        |
| check     | 检查依赖问题     | `pip check`              |
| cache     | 管理缓存       | `pip cache purge`        |
| download  | 下载包        | `pip download numpy`     |
| wheel     | 构建 wheel 包 | `pip wheel xxx`          |

会从 Python 官方软件仓库 **PyPI（Python Package Index）** 下载并安装 `requests` 库。

## 3 先检查是否安装 Python & pip

```sh
# 查看 Python 版本
python3 --version

# 查看 pip 版本
pip --version
```

## 4 pip 安装

### 4.1 macOS

```sh
brew install python
```

`Homebrew` 安装 `Python` 同时也会自动安装 `pip`

### 4.2 Linux 发行版

Debian / Ubuntu / Linux Mint:

```sh
sudo apt update
sudo apt install python3-pip

# 验证
pip3 --version
```

Fedora / RHEL Rocky Linux / AlmaLinux:

```sh
sudo dnf install python3-pip
```

Arch Linux / Arch Linux ARM / Manjaro:

Arch 默认 Python3。

```sh
sudo pacman -S python-pip
```

## 5 官方标准安装方式（推荐）

前提是要先安装 [Python](https://www.python.org/downloads/)

### 5.1 ensurepip（优先，Python 自带）

Python 自带一个[`ensurepip`](https://pip.pypa.io/en/latest/installation/#ensurepip)模块，可以在 Python 环境中安装 pip。

```sh
# Unix/Linux:
python -m ensurepip --upgrade

# Windows:
 py -m ensurepip --upgrade
```

### 5.2 get‑pip.py 引导脚本（离线 / 修复损坏 pip）

使用 [get-pip.py](https://pip.pypa.io/en/latest/installation/#get-pip-py) 官方安装脚本来安装 `pip`

 下载安装脚本：

```sh
wget https://bootstrap.pypa.io/get-pip.py
```

执行安装脚本：

```sh
# Unix/Linux
python get-pip.py

# Windows
py get-pip.py
```

## 6 pip 换源

替换为[清华镜像源](https://mirrors.tuna.tsinghua.edu.cn/help/pypi/) 为例

### 6.1 `index-url` 主源替换

**命令替换：**

```sh
# 临时替换：
pip install SomePackage -i https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple

# 永久替换：
# 更新 pip
python -m pip install --upgrade pip 
# 设置镜像源
pip config set global.index-url https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
```

**编辑配置文件替换：**

- Unix / Linux：编辑 `vim ~/.config/pip/pip.conf` 配置文件
- Windows：编辑文件 `%APPDATA%\pip\pip.ini` 

```sh
[global]
index-url = https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
```

将以上内容添加到 `pip.conf` 文件里

### 6.2 `extra‑index‑url` 附加备用源设置

如果您想配置多个镜像源平衡负载，可在已经替换 `index-url` 的情况下通过以下方式继续增加源站

需要 `pip>=21.2` 才支持 `extra‑index‑url`

**命令替换：**

```sh
pip config set global.extra-index-url "<url1> <url2>..."

# 如：
pip config set global.extra-index-url ""
```

**编辑配置文件替换：**

```sh
[global] index-url = https://pypi.tuna.tsinghua.edu.cn/simple extra-index-url = https://mirrors.aliyun.com/pypi/simple https://pypi.org/simple
```

区分两个参数：

- `index-url`：**主源**（默认下载包的仓库）
- `extra-index-url`：**附加备用源**，主源找不到包才去这里搜

国内镜像源地址：

- 阿里云 http://mirrors.aliyun.com/pypi/simple/
- 中国科技大学 https://mirrors.ustc.edu.cn/pypi/simple/
- 清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
- 清华大学 2 https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
- 腾讯源 http://mirrors.cloud.tencent.com/pypi/simple

## 7 包管理

### 7.1 pip install 安装包

用 `>=` `<=` `>` `<` `!=` 来指定包的版本，例如：

```sh
# Unix/Linux
python -m pip install SomePackage            # 最新版本
python -m pip install SomePackage==1.0.4     # 特定版本
python -m pip install 'SomePackage>=1.0.4'   # 不能低于指定版本
python -m pip install 'SomePackage>=2.0,<3.0'   # 安装版本范围: 2.x 版本

# Windows
py -m pip install SomePackage            # 最新版本
py -m pip install SomePackage==1.0.4     # 特定版本
py -m pip install 'SomePackage>=1.0.4'   # 不能低于指定版本
py -m pip install 'SomePackage>=2.0,<3.0'   # 安装版本范围: 2.x 版本
```

### 7.2 pip list 查看已安装的包

```sh
pip list

# 结果：
Package      Version
------------ -------
numpy        2.1.0
requests     2.32.0
```

### 7.3 pip freeze 查看当前环境安装列表

```sh
pip freeze

# 结果：
numpy==2.1.0
requests==2.32.0
flask==3.0.0
```

主要用于生成依赖文件。

根据软件列表安装包：

```sh
# 将输出结果导入文件
pip freeze > requirements.txt 

# 生成文件：
requirements.txt

# 内容：
requests==2.32.0
numpy==2.1.0

# 根据 Packers.txt 安装
pip install -r requirements.txt
```

常用于：
- 项目部署
- Docker
- 服务器环境复制

**pip list 与 pip freeze 区别：**

|命令|用途|格式|
|---|---|---|
|pip list|人查看|表格|
|pip freeze|程序使用|requirements格式|

### 7.4 pip upgrade 升级包

```sh
# 升级第三方库
python -m pip install --upgrade SomePackage
```

### 7.5 pip show 查看包详细信息

```sh
pip show requests

# 输出：
Name: requests
Version: 2.32.0
Location:
/usr/lib/python3/site-packages
Requires:
```

信息包含：

| 信息       | 作用   |
| -------- | ---- |
| Name     | 包名   |
| Version  | 版本   |
| Location | 安装位置 |
| Requires | 依赖   |

### 7.6 pip uninstall 卸载

```sh
pip uninstall requests
```

卸载 requests 及相关文件

### 7.7 升级 pip

```sh
# Unix/Linux
python -m pip install --upgrade pip

# 用清华源更新 pip
python -m pip install -i https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple --upgrade pip

# Windows
py -m pip install --upgrade pip
```

### 7.8 pip cache 缓存管理

```sh
# 查看缓存：
pip cache info

# 查看缓存目录：
pip cache dir

# 清理：
pip cache purge
```

### 7.9 pip 安装本地包

```sh
pip install ~/numpy-2.1.0-cp312.whl
```

## 8 pip 常用参数

| 参数                  | 作用      | 示例                                  |
| ------------------- | ------- | ----------------------------------- |
| `-U`                | 升级      | `pip install -U flask`              |
| `-q`                | 安静模式    | `pip install -q xxx`                |
| `-v`                | 显示详细信息  | `pip install -v xxx`                |
| `--user`            | 安装到用户目录 | `pip install --user xxx`            |
| `--force-reinstall` | 强制重新安装  | `pip install --force-reinstall xxx` |
| `--no-cache-dir`    | 不使用缓存   | `pip install --no-cache-dir xxx`    |
| `-r`                | 从文件安装   | `pip install -r requirements.txt`   |
| `-i`                | 指定源     | `pip install xxx -i URL`            |

### pip --user 用户安装

```sh
# 普通安装
pip install black
# 安装位置：
/usr/lib/python3.x/site-packages

# 用户安装：
pip install --user black
# 安装位置：
~/.local/lib/python3.x/site-packages
```

`--user` 会将安装位置改为 `~` 用户目录下

###  pip 与虚拟环境 venv

不推荐 `pip install flask` 直接安装，而是在项目环境下：

```sh
# 创建：
python -m venv .venv

# 进入环境
source .venv/bin/activate # Unix/Linux
.venv\Scripts\activate    # Windows

# 安装
pip install flask

# 退出环境
deactivate
```

## pip 常见问题

### pip 找不到命令

```sh
pip: command not found

解决：
python -m pip
```

### 权限错误

```sh
pip install --user package

# 或者使用：
venv
```

### pip 与 python 版本不匹配

```sh
python3 -m pip --version
```

## pip 工作流

### 创建 Python 项目

```sh
mkdir project
cd project

python -m venv .venv

source .venv/bin/activate

pip install requests

pip freeze > requirements.txt
```

### 别人拿到项目

```sh
git clone project

cd project

python -m venv .venv

source .venv/bin/activate

pip install -r requirements.txt
```

## 常用 pip 命令速查表

| 需求     | 命令                                |
| ------ | --------------------------------- |
| 安装包    | `pip install 包名`                  |
| 安装指定版本 | `pip install 包名==版本`              |
| 升级包    | `pip install -U 包名`               |
| 卸载包    | `pip uninstall 包名`                |
| 查看安装包  | `pip list`                        |
| 查看包详情  | `pip show 包名`                     |
| 导出依赖   | `pip freeze > requirements.txt`   |
| 安装依赖   | `pip install -r requirements.txt` |
| 更新 pip | `python -m pip install -U pip`    |
| 清理缓存   | `pip cache purge`                 |
| 用户安装   | `pip install --user 包名`           |

---

**一句话总结：**

> **pip 是 Python 的包管理器，用来从 PyPI 安装、升级、卸载和管理 Python 第三方库；实际开发中最常用的是 `pip install`、`pip freeze`、`pip install -r requirements.txt` 和配合 `venv` 创建隔离环境。**

详细文档：
1. [pip 安装页面](https://pip.pypa.io/en/latest/installation/)
2. [pip 快速上手](https://pip.pypa.io/en/stable/getting-started/)
3. [pip 用户手册](https://pip.pypa.io/en/stable/user_guide/)
4. [pypi 文档](https://pypi.org/)