# chgrp

用于修改文件或目录所属的用户组（group）。

## 语法

```sh
chgrp [options] GROUP FILE...
```

## 选项

| 参数   | 作用         | 示例                         |
| ---- | ---------- | -------------------------- |
| `-c` | 只显示发生变化的文件 | `chgrp -c developers file` |
| `-f` | 忽略错误信息     | `chgrp -f developers file` |
| `-v` | 显示详细操作过程   | `chgrp -v developers file` |
| `-R` | 递归修改目录及子文件 | `chgrp -R developers dir`  |

## 实例

### 1. 修改文件所属组

```sh
chgrp developers project.txt

# 查看:
ls -l project.txt

# 输出:
-rw-r--r-- 1 luis developers 2048 Aug 5 project.txt
```

### 2. 修改目录所属组

```sh
chgrp admin /data
```

### 3. 递归修改目录所有文件

```sh
chgrp -R developers /project

# 效果:project 目录内所有文件/文件夹的组都会改成 developers
```

