# find

`find` 是 Linux 中最强大的**文件搜索工具**之一，用于：

- 按名称查找文件
- 按类型查找文件
- 按大小查找文件
- 按时间查找文件
- 按权限查找文件
- 查找后执行命令

## 语法 

```shell
find [路径] [条件] [动作]
```

---

## 选项

### 1. 查找路径参数

|参数|作用|示例|
|---|---|---|
|`find .`|当前目录查找|`find . -name test.txt`|
|`find /path`|指定目录查找|`find /etc -name "*.conf"`|
|`find ~`|用户家目录查找|`find ~ -name "*.jpg"`|
|`find /`|全系统查找|`find / -name passwd`|

### 2. 名称匹配参数

| 参数       | 作用            | 示例                         |
| -------- | ------------- | -------------------------- |
| `-name`  | 按文件名搜索（区分大小写） | `find . -name "*.txt"`     |
| `-iname` | 按文件名搜索（忽略大小写） | `find . -iname "*.TXT"`    |
| `-path`  | 按完整路径搜索       | `find . -path "*/cache/*"` |
| `-ipath` | 路径搜索（忽略大小写）   | `find . -ipath "*IMG*"`    |
| `-regex` | 正则匹配路径        | `find . -regex ".*\.txt"`  |

### 3. 文件类型参数

| 参数        | 含义        | 示例                  |
| --------- | --------- | ------------------- |
| `-type f` | 普通文件      | `find . -type f`    |
| `-type d` | 目录        | `find . -type d`    |
| `-type l` | 符号链接      | `find . -type l`    |
| `-type b` | 块设备文件     | `find /dev -type b` |
| `-type c` | 字符设备文件    | `find /dev -type c` |
| `-type s` | socket 文件 | `find . -type s`    |

### 4. 文件大小参数

| 参数         | 作用   | 示例                   |
| ---------- | ---- | -------------------- |
| `-size n`  | 精确大小 | `find . -size 100M`  |
| `-size +n` | 大于大小 | `find . -size +100M` |
| `-size -n` | 小于大小 | `find . -size -10M`  |

**大小单位：**

| 单位  | 含义  |
| --- | --- |
| `c` | 字节  |
| `k` | KB  |
| `M` | MB  |
| `G` | GB  |

### 5. 用户 & 组参数

| 参数       | 作用     | 示例                        |
| -------- | ------ | ------------------------- |
| `-user`  | 指定用户文件 | `find /home -user luis`   |
| `-group` | 指定组文件  | `find /data -group users` |
| `-uid`   | 按 UID  | `find / -uid 1000`        |
| `-gid`   | 按 GID  | `find / -gid 1000`        |

### 6. 权限查找参数

| 参数            | 作用    | 示例                   |
| ------------- | ----- | -------------------- |
| `-perm`       | 按权限查找 | `find . -perm 755`   |
| `-readable`   | 可读文件  | `find . -readable`   |
| `-writable`   | 可写文件  | `find . -writable`   |
| `-executable` | 可执行文件 | `find . -executable` |

### 7. 时间查找参数

|时间|含义|
|---|---|
|atime|访问时间|
|mtime|修改时间|
|ctime|状态改变时间|
|`-mtime n`|n 天前修改|
|`-mtime +n`|超过 n 天|
|`-mtime -n`|n 天以内|
|`-mmin`|修改分钟|
|`-amin`|访问分钟|
|`-cmin`|状态改变分钟|

### 8. 逻辑组合参数

| 符号            | 作用     | 示例                               |
| ------------- | ------ | -------------------------------- |
| `-a` 或 `-and` | 并且（默认） | `-type f -name "*.txt"`          |
| `-o` 或 `-or`  | 或者     | `-name "*.jpg" -o -name "*.png"` |
| `!` 或 `-not`  | 取反     | `! -type d`                      |

---

## 二、实例

### 查找文件名：

```sh
find . -name file.txt
```

### 目录搜索

```sh
find . -type d
```

### 普通文件且 txt：

```sh
find . -type f -name "*.txt"
```

### 查找 jpg 或 png：

```sh
find . \
-name "*.jpg" \
-o \
-name "*.png"
```

### `-exec` 找到后执行命令。

```sh
find . -name "*.txt" -exec rm {} \;
# 相当于执行：
rm 文件1.txt
rm 文件2.txt

# 使用 `+` 批量执行
find . -name "*.txt" -exec rm {} +
# 相当于执行：
rm a.txt b.txt c.txt
```

`+` 的效率更高。

### 查找空文件

```sh
find . -type f -empty

# 删除空目录
find . -type d -empty
```

### `-delete` 删除文件/目录

```sh
find . -name "*.tmp" -delete
# 等价：
find . -name "*.tmp" -exec rm {} \;
```

### 限制搜索深度

```sh
# 最大深度：
find . -maxdepth 2 -name "*.lua"
# 只搜索:
当前目录
 └── 一级目录
      └── 二级目录

# 最小深度：
find . -mindepth 2 -name "file*.txt" # 会跳过当前目录。
```

### 按钮文件大小搜索

```sh
# 查找大于 1GB 文件：
find / -type f -size +1G

# 查找小于 10KB 文件：
find . -type f -size -10k
```

### 按时间搜索

```sh
# 查找最近 7 天修改文件：
find . -mtime -7

# 查找 30 天没有修改：
find . -mtime +30

# 最近一天：
find . -mtime -1

# 最近 10 分钟修改：
find . -mmin -10
```

### 权限搜索

```sh
# 查找 777 权限：
find . -perm 777

# 查找所有可执行文件：
find /usr/bin -executable
```

### 实际案例

查找超过 1GB 的文件：

```sh
find / -type f -size +1G
```

删除 30 天前日志：

```sh
find /var/log -name "*.log" -mtime +30 -delete
```

### 与其他工具组合

```sh
# 配合 grep 查找包含文字的文件：
find . -type f -name "*.conf" -exec grep "server" {} \;
# 流程：
find 找文件
 ↓ 
grep 搜内容

# 配合 xargs 大量文件推荐：
find . -name "*.txt" | xargs rm
# 流程：
find 输出文件列表
↓
xargs 拼接参数
↓
rm 文件1 文件2 文件3

# 解决文件名包含空格问题：
find . -name "*.txt" -print0 | xargs -0 rm
```
 
## 记忆口诀

```
-name     按名字找
-type     按类型找
-size     按大小找
-time     按时间找
-perm     按权限找
-user     按用户找
-exec     找到后执行
-delete   找到后删除
```

