# cat

concatenate 命令用于连接文件并打印到标准输出设备上，它的主要作用是用于查看和连接文件。

## 语法

```sh
cat [选项] [文件]
```

---

## 选项

| 选项   | 说明                |
| ---- | ----------------- |
| `-n` | 显示行号              |
| `-b` | 显示行号，但只对非空行进行编号。  |
| `-s` | 压缩连续的空行，只显示一个空行。  |
| `-E` | 在每一行的末尾显示 `$` 符号。 |
| `-T` | 将 Tab 字符显示为 `^I`。 |
| `-t` | 显示一些非打印字符。        |

---

## 实例

**01. 查看文件内容：** 显示文件 filename 的内容。

```sh
cat filename
```

**02. 创建文件：** 将标准输入重定向到文件 filename，覆盖该文件的内容。

```sh
cat > filename
```

**03. 追加内容到文件：** 将标准输入追加到文件 filename 的末尾。

```sh
cat >> filename
```

**04. 连接文件：** 将 file1 和 file2 的内容合并到 file3 中。

```sh
cat file1 file2 > file3
```

**05. 显示多个文件的内容：** 同时显示 file1 和 file2 的内容。

```sh
cat file1 file2
```

**06. 使用管道：** 将 cat 命令的输出作为另一个命令的输入。

```sh
cat filename | command
```

**07. 查看文件的最后几行：** 显示文件 filename 的最后 10 行。

```sh
cat filename | tail -n 10
```

**08.** 把 textfile1 的文档内容加上行号后输入 textfile2 这个文档里：

```sh
cat -n textfile1 > textfile2
```

**09.** 把 textfile1 和 textfile2 的文档内容加上行号（空白行不加）之后将内容附加到 textfile3 文档里：

```sh
cat -b textfile1 textfile2 >> textfile3
```

