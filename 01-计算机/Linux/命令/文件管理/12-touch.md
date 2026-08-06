创建文件(可用来修改文件的修改时间)

## 语法

```sh
touch [选项] 文件名
```

---

## 选项

| 参数            | 作用             | 示例                           |
| ------------- | -------------- | ---------------------------- |
| `-a`          | 只修改访问时间（atime） | `touch -a file`              |
| `-m`          | 只修改修改时间（mtime） | `touch -m file`              |
| `-c`          | 文件不存在时不创建      | `touch -c file`              |
| `-d`          | 使用指定时间修改时间戳    | `touch -d "2026-01-01" file` |
| `-t`          | 使用指定时间格式修改时间戳  | `touch -t 202608052000 file` |
| `-r`          | 使用另一个文件的时间戳    | `touch -r a.txt b.txt`       |
| `--time=WORD` | 指定修改时间类型       | `touch --time=access file`   |

---

## 实例

### 01 新建空文件

```sh
touch test.txt
```

### 02 新建多个文件

```sh
touch a.txt b.txt c.txt
```

### 03 创建指定路径文件

```sh
touch /tmp/demo/test.txt

# 如果目录不存在：
No such file or directory

# 请创建对应目录:
mkdir -p /tmp/demo
touch /tmp/demo/test.txt
```

### 04 用花括号创建多文件

使用花括号在 test 目录内创建多文件

```sh
touch test/{a.txt,b.txt,c.txt}
```

### 05 修改文件时间

touch 一个已经存在的文件，即更新修改时间

```sh
touch test.txt
```

只修改 `Access Time` 和 `Modify Time`

### 06 -a 只修改访问时间（atime）

```sh
touch -a test.txt
```

只修改 `Access Time` ，保持 `Modify Time` 不变

### 07 -m 只修改修改时间（mtime）

```sh
touch -m test.txt
```

只修改 `Modify Time` 时间

### 08 -c 不创建文件

默认不加 `-c` 参数，没有 `hello.txt` 文件，用 `touch hello.txt` 命令会创建 `hello.txt` 文件。

```sh
touch -c hello.txt
```

若用 `-c` 参数，没有 hello.txt 文件则不会创建。

此参数适合脚本检测：`touch -c /etc/config.conf`

### 09 批量创建编号文件

```sh
touch file{1..5}.txt

# 生成：
file1.txt
file2.txt
file3.txt
file4.txt
file5.txt
```