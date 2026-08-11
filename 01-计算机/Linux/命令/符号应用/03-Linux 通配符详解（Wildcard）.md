# Linux 通配符详解（Wildcard）

Linux 通配符（Wildcard）是 **Shell 用来匹配文件名的特殊字符**，主要用于批量操作文件。

相关文档:
- [[01-命令中括号的使用技巧详解]]
- [[02-Linux 中连接多个命令的符号详解]]

## 选项

| 通配符      | 名称   | 作用       | 示例          | 匹配                |
| -------- | ---- | -------- | ----------- | ----------------- |
| `*`      | 星号   | 匹配任意数量字符 | `*.txt`     | a.txt、hello.txt   |
| `?`      | 问号   | 匹配一个字符   | `?.txt`     | a.txt             |
| `[abc]`  | 字符列表 | 匹配其中一个字符 | `[abc].txt` | a.txt、b.txt       |
| `[a-z]`  | 字符范围 | 匹配范围内字符  | `[a-z].txt` | a.txt、z.txt       |
| `[!abc]` | 排除匹配 | 不匹配指定字符  | `[!a]*`     | b.txt、c.txt       |
| `~`      | 家目录  | 当前用户目录   | `~/file`    | `/home/user/file` |
|          |      |          |             |                   |

## 实例

### 1 `*` 匹配任意字符

```sh
*
```

匹配一个或多个字符，包括 0 个字符。

```sh
ls *.txt

# 展开：
file.txt test.txt a.txt

# 查找所有 jpg 图片
ls *.jpg

# 删除所有日志
rm *.log

# 匹配开头
ls log*

# 匹配结尾
ls *backup

# 删除当前所有文件
rm -rf *
```

#### 1.1 通配符不会匹配隐藏文件

```sh
ls *

# 不会显示隐藏文件:
.bashrc
.gitconfig
.profile
```

`*` 默认不会匹配以 `.` 开头的文件。隐藏文件可通过 `ls -a` 命令显示

### 2 `?` 单字符匹配

只能匹配 **一个字符**。


```sh
# 文件列表：
a.txt
b.txt
ab.txt
cd.txt
abc.txt
def.txt

# 匹配: a.txt b.txt
ls ?.txt

# 匹配：ab.txt cd.txt
ls ??.txt
```

### 3 `[ ]` 字符匹配

匹配指定字符

```sh
# 文件列表: 
a.txt
b.txt
c.txt
d.txt

# 匹配: a.txt b.txt c.txt 
ls [abc].txt
```

#### 3.1 `[a-z]` 字符范围匹配

```sh
ls [a-z].txt

# 匹配：
a.txt
b.txt
c.txt
...
z.txt
```

#### 3.2 `[0-9]` 数字范围匹配

```sh
ls file[0-9].txt

# 匹配：
file1.txt
file2.txt
...
file9.txt
```

#### 3.3 `[a-zA-Z]` 多个范围匹配

```sh
ls [a-zA-Z].txt

# 匹配：
a.txt
B.txt
z.txt
```

### 4 `[! ]` 排除匹配

不匹配括号中的字符

```sh
ls [!a].txt

# 显示：
b.txt
c.txt

# 不显示：
a.txt
```

### 5 `~` 家目录展开

代表家目录，用户登录目录

不是通配符，但是 Shell 常见展开。

```sh
cd ~

# 等价：
cd /home/user
```

#### 5.1 指定用户

```sh
# 进入 root 目录
cd ~root
```

