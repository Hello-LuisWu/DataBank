# Linux 中连接多个命令的符号详解

| 符号     | 名称                 | 作用                  | 示例                               |
| ------ | ------------------ | ------------------- | -------------------------------- |
| `;`    | 命令分隔符              | 无论成功失败都执行下一个命令      | `cmd1; cmd2`                     |
| `&&`   | 且 (AND)            | 前一个成功才执行下一个         | `make && make install`           |
| `\|\|` | 或(OR)              | 前一个失败才执行下一个         | `command \|\| echo "执行错误"`       |
| `&`    | 后台执行               | 将命令放后台运行            | `sleep 100 &`                    |
| `\|`   | 管道（标准输出）           | 将前命令的标准输出作为后命令的标准输入 | `ps aux \| grep python`          |
| `\`    | 续行符                | 多行写一个命令             | `echo hello \`                   |
| `{}`   | 当前 Shell 分组        | 当前 Shell 执行命令组      | `{ cd /tmp; ls; }`               |
| `<( )` | 进程替换               | 把命令结果当文件            | `diff <(ls a) <(ls b)`           |
| `>`    | 输出重定向              | 输出到文件               | `echo hi > a.txt`                |
| `>>`   | 追加重定向              | 追加输出                | `echo hi >> log`                 |
| `<`    | 标准输入重定向            | 把文件内容当作命令标准输入       | `cat < a.txt`                    |
| `<<`   | Here Document 文档传入 | 多行文本当作输入，自定义结束标记    | `cat <<EOF`                      |
| `<<<`  | here‑string 字符串传入  | 直接传入单行字符串           | `grep abc <<< "hello abc world"` |

---

## 实例

### 01 ; 顺序执行

```sh
mkdir test ; echo hello
```

- 第一个成功 → 执行第二个
- 第一个失败 → 仍然执行第二个

不管前面一个命令执行是否成功，对会执行下一个命令

### 02  && 成功后执行

常用于安装软件：

```sh
git clone repo && cd repo && make
```

逻辑：

```sh
command1 成功 
↓ 
command2
```


前面一个命令必须执行成功，才执行下一个

### 03 \|\| 失败后执行

```sh
mkdir test || echo "创建失败"
```

- test 不存在 → 创建成功，不输出
- test 已存在 → 输出失败

前面的命令只有执行失败才执行后面的命令

**组合 `&&` 和 `||`** : 

```sh
mkdir test && echo OK || echo ERROR
```

### 04 & 后台运行

```sh
sleep 100 & 

# 立即返回：
[1] 12345. # 其中 12345 = PID

# 查看后台：
jobs && ps

# 停止：
kill PID
```

**第一个后台执行：**

```sh
sleep 20 & echo "继续执行"
```

### 05 \| 管道

```sh
ls -l | grep sh

# 等价：
ls -l # 输出
↓
grep sh # 搜索
```

将前一个命令的标准输出，交给下个命令当作标准输入

```sh
stdout
 ↓
stdin
```

**查进程：**

```sh
ps aux | grep nginx
```

**统计：**

```sh
cat file.txt | wc -l
```

**排序：**

```sh
ls | sort
```

### 06 反斜杠 \\ 续行

用于长命令换行。

```sh
mkdir \
project \
src

# 等价：
mkdir project src
```

`\` 后面跟着换行符，将长命令拆分为多行，提高可读性（反斜杠后不能有空格）。

### 07 \> 覆盖重定向

```sh
echo hello > file
```

**覆盖原理：**

将 echo 输出的内容输入到 file 文件内，file 原有的内容会被删除

### 08 \>\> 追加重定向

```sh
# 没定向前文件内容为：hello
echo world >> file

# 定向后的 file 文件内容为：
hello
world
```

将 echo 的输出内容 追加到 file 文件的末尾（会在最后一行向下新开一行）

### 09 \< 输入重定向

将文件一行或多行内容作为 command 的标准输入。

```sh
cat < test.txt

# 执行流程：
test.txt (文件内容)
    ↓
 stdin
    ↓
  cat
```

**应用场景:**

```sh
# sort 按字母排序行：
sort < file.txt

# wc 统计行数：
wc -l < file.txt

# mysql 导入数据库
mysql database < backup.sql

# 读取配置
read name < user.txt
```

### 10 \<\< Here Document（文档输入）

向命令提供一段多行输入文本。

```sh
cat << EOF
hello
linux
shell
EOF

# 输出：
hello
linux
shell
```

原理是 `shell` 会把 `EOF` 标记内的多行内容临时保存，然后传递给 `bat` 命令.  类似于 `cat < 临时文件`

#### EOF 是什么？

- EOF = **End‑Of‑File（文件结束标记）**，并不是 shell 内置关键字、特殊符号。  
- 它只是一个普通自定义标签（约定俗成的名字），你可以换成 END、STOP、QUIT。  
- EOF 用于标记多行文本开头和结尾

```sh
cat > EOF(开始标记)
...
	...
...
EOF(结束标记)
```

开始标记要与结束标记一致

标记可以不写 `EOF` , 可以随意定义字符，一个字符或多个字符，大写字母或小写字母，数字或字母

```sh
# 小写字母
cat << aaa
...
aaa

# 大写字母
cat << BBB
...
BBB

# 数字
cat << 123
...
123

# 混合写法
cat << Abc1
...
Abc1
```

#### 应用场景

```sh
# 创建文件
cat > test.txt << EOF
hello
linux
shell
EOF

# 写配置文件
cat > nginx.conf << EOF
server {
    listen 80;
    server_name example.com;
}
EOF

# Shell 脚本菜单
cat << EOF
请选择：

1. 安装
2. 删除
3. 退出

EOF

# SSH 执行远程命令
ssh server << EOF
cd /tmp
ls
pwd
EOF

# 变量展开区别
name="Luis"
cat << EOF
Hello $name
EOF

# 创建配置文件
cat > ~/.config/app.conf << EOF
name=luis
port=8080
debug=true
EOF

# Docker 构建
docker exec -i mysql mysql << EOF
CREATE DATABASE test;
EOF

# 自动化部署
ssh 192.168.1.35 << EOF
git pull
systemctl restart nginx
EOF

# 生成脚本
cat > install.sh << 'EOF'
#!/bin/bash

echo "install"
EOF
# 这里使用：<< 'EOF' 避免变量提前展开。
```

### 11 \<\<\< 单行字符串输入

```sh
grep hello <<< "hello linux"

# 等价：
echo "hello linux" | grep hello
```

### 三者区别

| 特点     | `<`   | `<<`          | `<<<`       |
| ------ | ----- | ------------- | ----------- |
| 名称     | 输入重定向 | Here Document | Here String |
| 输入来源   | 文件    | 文本块           | 手动输入        |
| 是否需要文件 | 需要    | 不需要           | 不需要         |
| 适合     | 读取文件  | 生成多行输入        | 文本查找        |
| 数据位置   | 外部文件  | 命令内部          | 手动输入        |
| 常用于脚本  | 一般    | 非常常见          | 不常用         |

```sh
                 stdin

                   |
        -----------------------
        |          |          |
        <          <<         <<<
        |          |          |
      文件      多行文本    字符串


sort < a.txt


cat << EOF
hello
EOF


grep x <<< "xxx"
```

## 实际案例

```sh
# 经典的“成功则执行，失败则报错”链式写法
./configure && make && sudo make install || echo "安装失败，请检查日志"
```

## 命令连接符思维图

```
                 Shell 命令连接

                         |
        ---------------------------------
        |               |               |
     顺序控制         数据传递          环境控制

        |               |               |
     ; && ||            |             () {}
                       管道
                        |
                        |
                        |

                     输出控制
                     > >> < << <<<

	        命令生成
	        $( )
	        <( )
```