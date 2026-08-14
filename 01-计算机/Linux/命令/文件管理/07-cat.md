# cat

`cat`（concatenate）意为**连接、拼接**，用于：

- 查看文件内容
- 合并多个文件
- 创建文件
- 追加文件内容
- 配合重定向处理文本

## 语法

```sh
cat [选项] [文件]
```

---

## 选项

|参数|全称|作用|示例|
|---|---|---|---|
|`-n`|number|显示所有行号|`cat -n file.txt`|
|`-b`|number-nonblank|只给非空行编号|`cat -b file.txt`|
|`-s`|squeeze-blank|压缩连续空行|`cat -s file.txt`|
|`-A`|show-all|显示所有隐藏字符|`cat -A file.txt`|
|`-E`|show-ends|显示行尾 `$`|`cat -E file.txt`|
|`-T`|show-tabs|显示 Tab 字符|`cat -T file.txt`|
|`-v`|show-nonprinting|显示不可打印字符|`cat -v file.txt`|
|`-e`|等价 `-vE`|显示特殊字符和行尾|`cat -e file.txt`|
|`-t`|等价 `-vT`|显示 Tab 和特殊字符|`cat -t file.txt`|

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

