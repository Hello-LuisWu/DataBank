# Linux 环境变量详解

Linux 中的变量（Variable）是 Shell 脚本和命令行中存储数据的重要机制。

变量可以保存：
- 字符串
- 数字
- 路径
- 命令结果
- 系统环境信息
- 用户配置

---

## 🎹 1 Shell 变量类型总表

| 类型       | 作用域                             | 写法                  | 关键点                              | 示例                           |
| -------- | ------------------------------- | ------------------- | -------------------------------- | ---------------------------- |
| 普通(本地)变量 | 仅在**当前 Shell 进程**中有效            | `name=value`        | 最常用，通过直接赋值创建，对子(shell)进程不可见      | `name="Luis"`                |
| 环境变量     | 在当前 Shell **及其所有子(shell)进程**中有效 | `export name=value` | 通常用 `export` 导出，用于系统配置和程序通信      | `export PATH=$PATH:/opt/bin` |
| 局部变量     | 仅在**函数内部**有效                    | 函数内部变量              | 使用 `local` 关键字声明，防止变量污染函数外部环境    | `local name=test`            |
| 位置参数     | 用于**脚本或函数**，代表命令行传入的参数          | `$1 $2 ...`         | `$1`, `$2` ... `$9` 分别表示第1到第9个参数 | `$1`                         |
| 特殊变量     | Shell 预定义的、有特殊用途的变量             | `$? $$ $!`          | 用于获取脚本名称、参数个数、命令退出状态等            | `$?`                         |
| 只读变量     | 值**不可修改**的变量                    | `readonly`          | 使用 `readonly` 声明，常用于定义常量         | `readonly PI=3.14`           |
| 数组变量     |                                 | `array=(a b c)`     | 保存多个值                            | `${array[0]}`                |
| 命令替换变量   |                                 | `$(command)`        | 保存命令输出                           | `date=$(date)`               |
| 默认变量     |                                 | `${var:-value}`     | 设置默认值                            | `${name:-Linux}`             |

---

## ✍️ 2 变量定义

**基本语法**：变量名后紧跟等号，再跟值，等号两边不能有空格

```sh
变量名=变量值

# 错误：= 两边有空格会报错
my_name = "John" # 视为三个命令
# shell 会认为：
name
=
Linux

# 正确: = 两边不能有空格
my_name="John"
```

### 2.1 命名规则

- 只能由**字母、数字和下划线**组成。
- 不能以**数字**开头。
- 大小写敏感，`myVar` 和 `myvar` 是不同的变量。
- 建议使用有意义的名称，环境变量常用大写，自定义变量建议用小写。

```sh
# 正确格式：
name
user_name
PATH
_version

# 错误格式：
1name
user-name
user.name
```

### 2.2 定义普通变量

```sh
name"Luis"
```

### 2.3 定义环境变量

```sh
export name="Luis"
```

### 2.4 普通变量与环境变量的区别

子(程序) shell 不能共享本地变量，环境变量对当前SHELL及其子SHELL有效。

```sh
# 普通变量：
name="luis"
bash # 进入子(shell)进程 
echo $name # 子进程无法调用普通变量
#结果：
空值

# 用 export 设置环境变量
export name="luis"
bash # 进入子(shell)进程
echo $name # 子进程可以调用变量值
# 结果：
luis
```

### 2.5 变量引用

变量引用就是在变量名前加 `$`，例如 `echo $name`，Shell 会将 `$name` 展开为变量中保存的值。  

```sh
# 定义变量
name="Luis"

# 打印变量
echo $name # $ 后面紧跟变量名

# 结果：
Luis
```

### 2.6 定义中符号的使用

- **无引号** `$var` ：**基本引用**，变量值中没有特殊字符时可以省略引号。`my_var=hello`
- **单引号** `'$ver'` ：**强引用**，原样输出引号内的所有内容，变量名会被当做普通字符处理。`literal='Hello, $my_name'`（输出 `Hello, $my_name`）
- **双引号** `"$var"` ：**弱引用**，允许解析内部的变量和命令。`greeting="Hello, $my_name"`
- **花括号** `${var}` ： **精确引用**，明确变量名边界，变量名后面紧跟其他字符时可使用，另有其他扩展操作

```sh
# 无特殊字符和空格可不加引号：
name=luis
#打印：
echo name
# 结果：
hello

# ' 单引号
name='$hello @luis' # 有特殊字符可用单引号
# 打印：
echo $name
# 结果：
$hello @luis # 变量名会被当做普通字符处理

# " 双引号
age="14"
name="luis $age" # 引用变量名用双引号
# 打印：
echo $name
# 结果：
luis 14 # 会解析内部变量名

# 花括号
name="Luis"
info=${name}-14 # 变量名后面紧跟其他字符时可使用
#打印：
echo $info
# 结果：
Luis-14

# 花括号扩展
name=""
info=${name:-"No User"}-14 # 扩展操作：若无变量就启用默认值
# name: 未赋值或未创建的变量
# :-"No User"：设置的默认值。若查找不到变量或变量值，则显示默认值。
#打印：
echo $info
# 结果：
No User-14
```

### 2.7 环境变量的持久化

 变量周期分类:
1. **零时变量**: 用 `export` 命令在终端临时声明，重启 Shell 变量就会失效
2. **永久变量**: 将 `export` 命令写入配置文件，
	- 用户级配置文件所在:  `~/.bashrc`, `~/.profile`, `~/.bash_profile`, `~/.zshrc`, 添加后则永久生效
	- 系统级配置文件所在: `/etc/profile`, `/etc/environment`

变量作用域分类:
1. **用户级**: 仅对设置这个变量的用户生效,对其他用户则无效
    - 变量写进用户目录下的`~/.bash_profile` 或 `~/.zshrc` 文件里面
    - `.bash_profile` 是针对 bash , `.zshrc` 是针对 zsh .
2. **系统级**: 对该系统中所有用户都有效
    - 变量写进 `/etc` 目录下的 `profile`, 或 `environment` 文件里面

### 2.8 只读变量 readonly

```sh
readonly VERSION="1.0"

# 尝试修改：
VERSION="2.0"

# 结果：
bash: VERSION: readonly variable
```

### 2.9 数组变量

```sh
arr=("Linux" "Shell" "Bash")

# 获取第一个元素
echo ${arr[1]}
# 结果：
Linux

# 获取所有变量
echo ${arr[@]}
# 结果：
Linux Shell Bash

# 获取长度：
echo ${#arr[@]}
# 结果：
3
```

### 2.10 命令替换变量 

将命令结果保存

```sh
dir=$(pwd)

echo $dir
```

---

## ➕ 3 变量追加

### 3.1 普通追加

```sh
name"luis"

# 普通追加：
name="$name Shell"

# 展开：
name="luis Shell"
```

### 3.2 使用 `+=` 追加（推荐）

```sh
name="Luis"

# 追加：空格也会被追加
name+=" Wu"

# 展开：
name="Luis Wu"
```

### 3.3 追加数字变量

```sh
num=10

num+=20

echo $num

# 结果：
1020
```

不是数字相加，因为 `+=` 默认是字符串连接

**数学计算：**

```sh
num=10

((num+=20))

echo $num

# 结果：
30
```

### 3.4 多行变量追加

方法一：`+=`

```sh
text="Line1"

text+="
Line2"

echo "$text"

# 结果：
Line1
Line2
```

方法二：Here Document

```sh
text=$(cat <<EOF
Line1
Line2
Line3
EOF
)

echo "$text"

# 结果:
Line1
Line2
Line3
```


### 2.5 PATH 追加

`PATH` 变量用于指定 Shell 查找和执行命令时搜索的目录列表。 `PATH` 是系统自带的环境变量。

1. 若直接赋值 `export "PATH=/.../.../..."`, 这样新添加的值会覆盖 `PATH` 之前的所有的值
2. 若想不覆盖就要书写成 `export PATH="/.../...:${PATH}"`
	 - `$PATH` 的作用是新加的路径也包含自己, 相当于引用了 `PATH` 自己现有的变量值
	- `:` 是变量分隔符。作用是变量值的追加， 如: `export PATH="${HOME}/nvim/:/tmp/:${PATH}"` , `:` 后面加上自己要追加的变量或变量值, 新加的变量会追加到 `PATH` 里面

#### 常见的工具命令目录

**cargo（rust）：**

```sh
export PATH="${HOME}/.cargo/bin:${PATH}"
```

**go：**

```sh
export PATH="${PATH}:${HOME}/go/bin"
```

**Node 全局包：**

```sh
export PATH="${PATH}:${HOME}/.npm-global/bin"
```

**用户脚本目录：**

```sh
export PATH="${HOME}/bin:${PATH}"
```

在 PATH 后面追加：

```sh
export PATH="${PATH}:${HOME}/bin"
```

**避免 PATH 重复追加：**

```sh
# 如果每次打开终端都会加载：
export PATH="${PATH}:${HOME}/bin"

# 第一次：
/usr/bin:/home/user/bin
# 第二次：
/usr/bin:/home/user/bin:/home/user/bin
# 第三次：
/usr/bin:/home/user/bin:/home/user/bin:/home/user/bin

# 判断是否重复加载:
if [[ ":$PATH:" != *":$HOME/bin:"* ]]; then
    export PATH="${HOME}/bin:${PATH}"
fi
```

**使用 `${}` 添加多个目录：**

例如：

```sh
export PATH="${HOME}/bin:${HOME}/.local/bin:${PATH}"
```

> 不想重复设置可以将命令写在`.zshrc` / `.bashrc` 配置文件内

---

## 🔍 4 变量的引用与操作详解

在变量名前加 `$` 符号[](https://cloud.tencent.com.cn/developer/article/1960147?policyId=1003)。推荐使用 `${variable}` 的形式，它能明确变量边界，例如 `echo "${name}123"`，这样可以避免变量名与后面的字符串混淆，并支持高级操作[](https://bbs.huaweicloud.com/blogs/465171)。
### 4.1 加不加花括号的区别：

```sh
name="LuisWu"

# 不可花括号 ${xxx}
echo "$name_hello"
# 结果：
# $name和_hello 被视为一个整体，也就是变量名为 $name_hello 的变量。而此变量并未定义，所以无法打印变量值

# 加了花括号 ${xxx}
echo "${name}_hello" # 变量名用花括号括起来，规定了变量名的边界在哪。边界以外的字符无法影响内部的变量名
# 结果：
luisWu_hello 
```

### 4.2 变量引用中的引号

引用的引号作用和赋值引号的作用类似，详情请查看：[定义中符号的使用](#2.6 定义中符号的使用)

```sh
name="luis"

#无引号: 正常打印字符和变量
echo hello ${name}
# 结果：
hello luis

# 单引号：单引号内变量名会被当做普通字符处理
echo 'hello ${name}'
# 结果：不会展开变量。
hello ${name}

# 双引号：双引号内的变量名可以被正常引用
echo "hello ${name}"
# 结果：
hello luis
```

**字符串操作**：使用 `${ }` 可以进行丰富的字符串处理[](https://developer.aliyun.com/article/1263598)
- 获取变量长度：`${#变量名}`
- **截取子串**：`${变量名:起始位置:长度}`
- **删除匹配**：
    - `#` 从开头删除最短匹配[](https://developer.aliyun.com/article/1263598)
    - `##` 从开头删除最长匹配[](https://developer.aliyun.com/article/1263598)
    - `%` 从结尾删除最短匹配[](https://developer.aliyun.com/article/1263598)
    - `%%` 从结尾删除最长匹配[](https://developer.aliyun.com/article/1263598)
- **查找替换**：
    - `/` 替换第一个匹配[](https://developer.aliyun.com/article/1263598)
    - `//` 替换**所有**匹配[](https://developer.aliyun.com/article/1263598)

示例：

```sh
url="www.example.com"

# ${#变量名} 获取变量长度
echo ${#url}          # 输出: 15

# 从开头删除最短匹配
echo ${url#www.}      # 输出: example.com

# 从开头删除最长匹配
echo $**{**url##*.}   # 输出：com

# 从结尾删除最短匹配
echo ${url%.com}      # 输出: www.example

# 从结尾删除最长匹配
echo ${url%%.*}       # 输出：www

# 替换第一个匹配
echo ${url/./_}       # 输出: www_example.com (替换第一个)

# 替换所有匹配
echo ${url//./_}      # 输出: www_example_com (替换所有)
```

### 4.3 截取字符串

```sh
name="LuiaWu"


echo ${name:3} 
# 第一个字母从 0 开始计算：
# L u i s W u
# 0 1 2 3 4 5

# 结果：
aWu
```

 指定长度：

```sh
name="LuiaWu"


echo ${name:1:3}
# 第一个字母从 0 开始计算：从第 1 个字符截取到第 3 个字符
# L u i s W u
# 0 1 2 3 4 5

# 结果：
uia
```

### 4.4 变量默认值 `${var:-default}`

如果变量不存在或为空，使用默认值

```sh
echo ${name:-"Not Value"}

# 如果 name 不存在则输出：
Not Value
```

**例子：**

克隆到指定目录

```sh
     git clone https://www.xx.com/x/x ${plugin:-~/.aa/bb}/cc/dd
```

执行过程：
- 克隆过程中先查找 `plugin` 变量的路径
- 如果 `plugin` 不存在则使用 `:-~/.aa/bb` 作为默认值
- 使用默认值不会创建或赋值 `plugin` 变量。
- 默认值仅在当前命令中起作用

### 4.5 不存在则赋值 `${vsr:=value}` 

```sh
echo ${name:="Luis Wu"}

# 如果 name 不存在则赋值 name：
Luis Wu
```

**常用于自动创建脚本：**

```sh
CONFIG=${CONFIG:=config.ini}
```

如果用户没有设置就自动设置

区别：
- `:-` 只显示而不是修改
- `:=` 会修改变量

### 4.6 存在判断 `${var:+value}`

变量存在且非空时，使用替代值。

```sh
name="luis"

echo ${name:+haifeng} # 如果 name 存在且非空值。则使用 haifeng 值

# 结果：
haifeng
```

只显示而不修改变量值

### 4.7 错误检查 `${var:?error}`

指定报错信息

```sh
echo ${Name:?No User}

#显示：若变量不存在则报错
zsh: Name: No Luis
```

**脚本常用：**

```sh
FILE=${1:?请输入文件名}
```

如果没有参数, 提示错误。

### 4.8 字符串替换

```sh
# 格式：${变量/旧变量值/新变量值}

name="Luis Wu"

#打印
echo ${name/Luis Wu/Junli dong}

# 输出：
Junli dong
```

**替换全部:**

```sh
name="Luis Luis"

# 打印：
echo ${name//Luis/Jinli dong}

# 结果：
Junli dong Junli dong
```

### 4.9 数学运算

由于 Shell 变量默认是字符串[](https://developer.aliyun.com/article/482020)，需用特殊方式运算。

```sh
a=5
b=3
echo $((a + b))   # 输出 8
echo $[a * b]     # 输出 15[reference:54]
echo `expr $a / $b` # 输出 1[reference:55]
let "c = a - b"   # 变量 c 的值为 2[reference:56]
```

### 4.10 例子

 **设置代理：**

```sh
export HTTP_PROXY=http://127.0.0.1:7890
export HTTPS_PROXY=http://127.0.0.1:7890
```

**自动备份：**

```sh
file="config"
backup=${file}.bak
echo $backup

# 输出：
config.bak
```

**获取文件名和扩展名：**

```sh
file="/home/user/test.tar.gz"

name=${file##*/}
ext=${name##*.}

echo $name
echo $ext

# 结果：
test.tar.gz
gz
```


**自动设置默认编辑器：**

```sh
EDITOR=${EDITOR:-vim}
echo $EDITOR

# 结果：
vim
```

**检查脚本参数：**

```sh
FILE=${1:?请输入文件}

# 执行：
./backup.sh

# 输出：
请输入文件
```

### 4.11 变量引用记忆图

```sh
变量引用

$
├── $var
│
├── ${var}
│
├── 默认值
│   ├── ${var:-value}
│   ├── ${var:=value}
│   ├── ${var:+value}
│   └── ${var:?error}
│
├── 字符串处理
│   ├── ${#var}
│   ├── ${var:start:length}
│   ├── ${var#pattern}
│   ├── ${var##pattern}
│   ├── ${var%pattern}
│   └── ${var//old/new}
│
└── 特殊变量
    ├── $?
    ├── $$
    ├── $!
    └── $@
```

### 4.12 最常用的 10 个变量引用

| 优先级 | 写法                    | 用途      |
| --- | --------------------- | ------- |
| 1   | `$var`                | 读取变量    |
| 2   | `${var}`              | 安全引用    |
| 3   | `"$var"`              | 保留空格    |
| 4   | `${var:-default}`     | 默认值     |
| 5   | `${#var}`             | 长度      |
| 6   | `${var:start:length}` | 截取      |
| 7   | `${var##*/}`          | 获取文件名   |
| 8   | `${var%.*}`           | 获取文件名主体 |
| 9   | `${var//a/b}`         | 替换      |
| 10  | `$@`                  | 脚本参数    |

---
## 📺 5 显示变量

### 5.1 set 查看所有变量

```sh
set

# 结果：
HOME=/home/luis
PATH=/usr/bin
USER=luis
```

### 5.2 `env`/`printenv` 查看环境变量

```sh
env
```

### 5.3 `printenv` 查看单个变量

```sh
name="Luis"

# 查看：
printenv name

# 结果：
Luis
```

## 🧨 6 删除变量

```sh
unset 变量名
```

## ⌨️ 7 变量相关命令

- `env`: 查看当前用户全部的环境变量 
- `printenv`: 同 `env`, 查看指定环境变量的值
- `set`：显示当前 Shell 的**所有变量**（包括环境变量、本地变量和函数）
- `export`: 显示变量；将定义本地变量导出为**环境变量**, 设置环境变量
- `unset`: 清除指定环境变量

## 💻 8 系统自带环境变量

### 8.1 核心基础环境变量（最常用）

| 变量          | 含义                       | 示例值                            |
| ----------- | ------------------------ | ------------------------------ |
| `$PATH`     | 决定了 shell 将到哪些目录中寻找命令或程序 | `/usr/local/bin:/usr/bin:/bin` |
| `$HOME`     | 用户的家目录                   | `/home/username`               |
| `HOST`      | 主机的名称                    | `HOST`                         |
| `$USER`     | 用户登录的用户名                 | `root`                         |
| `$PWD`      | 当前工作目录的绝对路径。             | `/var/www/html`                |
| `$LOGNAME`  | 用户的登录名(等同于 USER)         | `root`                         |
| `$LANG`     | 定义系统的语言、地区和字符编码。         | `en_US.UTF-8`                  |
| `$UID`      | 当前用户 UID                 | `1000`                         |
| `$GID`      | 用户主组 GID                 | `1002`                         |
| `$OLDPWD`   | 上一次所在工作目录，`cd -` 依靠它     | `/home/luis/.config/nvim`      |
| `$OSTYPE`   | 操作系统类型                   | `linux-gnu`                    |
| `$MACHTYPE` | 完整机器架构信息                 | `x86_64` 或 `aarch64`           |

### 8.2 终端 & 会话相关变量

| 变量              | 含义                          | 示例值                           |
| --------------- | --------------------------- | ----------------------------- |
| `$SHELL`        | 当前使用的 Shell                 | `/bin/zsh`                    |
| `$TERM`         | 当前终端类型                      | `xterm-256color`              |
| `$LINES`        | 终端窗口行数                      | `24`                          |
| `$COLUMNS`      | 终端窗口列数                      | `80`                          |
| `$PS1`          | 主命令提示  （最常改）                | `%n@%m:%~%#`                  |
| `$PS2`          | 换行续行提示符 >                   | `%{%}∙%{%}`                   |
| `$PS3`          | select 循环菜单提示符              | `?#`                          |
| `$PS4`          | 调试 set -x 提示符               | `+%N:%i>`                     |
| `$HISTSIZE`     | shell 内存历史命令条数上限            | `10000`                       |
| `$HISTFILESIZE` | 历史文件 .bash_history 最大行数     | `2000`                        |
| `$HISTFILE`     | shell 历史记录文件所在              | `/home/luis-pc/.bash_history` |
| `$HISTCONTROL`  | 历史过滤：ignorespace、ignoredups | `ignoreboth`                  |
| `$HISTCMD`      | 显示当前命令在历史列表中的序号             | `153`                         |
| `$TMOUT`        | shell 闲置超时自动退出 (秒)，0 关闭     | `0`                           |

### 8.3 语言、本地化 LANG 系列

| 变量             | 含义          | 示例值           |
| -------------- | ----------- | ------------- |
| `$LANG`        | 系统全局语言编码    | `en_US.UTF-8` |
| `$LC_ALL`      | 强制覆盖所有本地化设置 | `en_US.UTF-8` |
| `$LC_TIME`     | 时间日期格式      | `en_US.UTF-8` |
| `$LC_NUMERIC`  | 数字格式        | `en_US.UTF-8` |
| `$LC_MESSAGES` | 程序提示语言      | `en_US.UTF-8` |

### 8.4 文件、目录、编辑器相关

需要自己定义

| 变量                 | 用途                |
| ------------------ | ----------------- |
| `$EDITOR`          | 默认文本编辑器 nano/vim  |
| `$VISUAL`          | 可视化编辑器            |
| `$PAGER`           | 分页查看器 less/more   |
| `$LD_LIBRARY_PATH` | 动态库搜索路径（非常重要）     |
| `$LD_PRELOAD`      | 预加载指定 so 动态库      |
| `$TMPDIR`          | 临时文件目录，默认 /tmp    |
| `$CDPATH`          | cd 命令搜索目录，快速进入文件夹 |

### 8.5 网络、用户会话变量（ssh 登录会携带）

| 变量                | 说明                  | 示例值                                    |
| ----------------- | ------------------- | -------------------------------------- |
| `$SSH_CONNECTION` | 客户端 IP 端口 服务端 IP 端口 | `192.168.28.1 50180 192.168.28.130 22` |
| `$SSH_CLIENT`     | ssh 客户端地址端口         | `192.168.28.1 50180 22`                |
| `$SSH_TTY`        | ssh 终端设备            | `/dev/pts/0`                           |
| `$DISPLAY`        | X11 图形显示器编号 :0      |                                        |
| `$XAUTHORITY`     | X‑Window 授权文件       |                                        |

### 8.6 邮件、历史、信号、高级 bash 变量

| 变量              | 释义                          |     |
| --------------- | --------------------------- | --- |
| `$MAIL`         | 用户邮件文件 /var/spool/mail/root |     |
| `$MAILCHECK`    | 多久检查一次新邮件 (秒)               |     |
| `$SHLVL`        | shell 嵌套层级；打开 bash 一次 + 1   |     |
| `$BASH`         | bash 程序路径 /bin/bash         |     |
| `$BASH_VERSION` | bash 版本字符串                  |     |
| `$BASH_ARGV`    | 数组，保存脚本全部参数                 |     |
| `$BASH_SOURCE`  | 数组，当前执行脚本路径                 |     |
| `$RANDOM`       | 只读变量，每次读取返回 0‑32767 随机整数    |     |
| `$SECONDS`      | shell 启动之后经过的总秒数            |     |
| `$PIPESTATUS`   | 管道每条命令返回码数组                 |     |

### 8.7 Bash 内置只读特殊变量（Shell 参数变量，环境变量）

这些只读，不能手动赋值；脚本编程高频, 这些是 Shell 内置的，用于获取脚本自身信息和运行状态的变量

| 变量                 | 含义                                     | 示例值          |
| ------------------ | -------------------------------------- | ------------ |
| `$0`               | 当前脚本 / Shell 程序名称                      | `zsh`        |
| `$1 $2 … $9 ${10}` | 脚本第 1、2… 参数                            |              |
| `$#`               | 传入脚本参数总个数                              | `0`          |
| `$*`               | 全部参数，IFS 拼接成单个字符串                      |              |
| `$@`               | 全部独立参数（推荐，可处理空格文件名）                    |              |
| `$?`               | 上一条命令退出返回码，0 = 成功                      | `0`          |
| `$$`               | 当前 Shell PID 进程号                       | `101350`     |
| `$!`               | 最近一条后台运行进程 PID                         | `0`          |
| `$_`               | 上一条命令最后的参数                             | `0`          |
| `$-`               | 当前 Shell 的**选项标志**, 如 `-i` 表示交互式 Shell | `569XZghims` |

实操例子 test.sh

```sh
#!/bin/bash
echo 脚本名:$0
echo 参数一:$1
echo 参数总数:$#
echo 所有参数:"$@"
echo 上条命令状态:$?
echo shell进程id:$$
```

## 💡 9 最佳实践与注意事项

- **变量命名**：遵循命名规范，使用有意义的名称，避免使用保留关键字。
- **安全第一**：引用变量时，养成用双引号包裹的习惯，如 `"$var"`，可以防止因变量值为空或包含空格而导致的意外错误。
- **作用域意识**：明确变量是本地、环境还是局部的，避免在函数中无意修改了全局变量[](https://www.300.cn/itzspd/587716.html)。
- **调试帮手**：善用 `set -x` 开启命令跟踪，可以清晰地看到脚本执行时变量的值，极大方便调试。