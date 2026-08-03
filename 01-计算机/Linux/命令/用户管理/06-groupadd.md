# groupadd

添加用户组, 是 Linux 用户权限管理中的基础命令。

相关文件:

```sh
/etc/group 组账户信息。
/etc/gshadow 安全组账户信息。
/etc/login.defs Shadow密码套件配置。
```

## 语法

```sh
groupadd [选项] 组名
```

## 选项

| 选项            | 说明                                                                                                        |
|-----------------|-------------------------------------------------------------------------------------------------------------|
| `-g 2000`       | 指定组的 GID                                                                                                |
| `-f`(`--force`) | 如果组已存在, 不报错退出, 与 -g 使用时，并且指定的 GID_MIN 已经存在时，选择另一个唯一的 GID（即 -g 关闭）。 |
| `-r`            | 创建系统组，系统组的 ID 小于 500                                                                            |
| `-K`            | 覆盖配置文件 /etc/login.defs                                                                                |
| `-o`            | 允许创建重复 GID 的组                                                                                       |

## 实例

### 01 -g 创建组并添加 GID

```sh
＃groupadd －g 2000 luis

# 查看:
getent group luis

# 输出:
luis:x:2000:
```

**为啥指定 GID?**

Linux 权限实际使用数字：文件:

```sh
-rw-r----- 1 luis developers test.txt
```

系统内部：

```sh
uid=1000
gid=2000
```

如果多台服务器共享文件：

```sh
# 服务器 A:
developers=2000

# 服务器 B:
developers=3000
```

权限可能混乱。所以服务器环境常固定 GID。

### 02 -f 强制创建重复组

```sh
sudo groupadd developers

# 如果组已经存在
groupadd: group 'developers' already exists

# 加 -f 强制创建不会报错
sudo groupadd -f developers
```

**应用场景:**

```sh
# 脚本自动部署：


#!/bin/bash

groupadd -f docker
```

无论执行多少次都安全。

### 03 -r 创建系统组

```sh
sudo groupadd -r nginx

# 查看
getent group nginx

# 结果:
nginx:x:995:
```

特点：
- GID 通常小于普通用户组
- 给系统服务使用

**常见系统组:**

| 服务   | 用户组 |
|--------|--------|
| nginx  | nginx  |
| mysql  | mysql  |
| docker | docker |
| ssh    | ssh    |

### 04 -o 允许重复 GID

```sh
sudo groupadd -g 1000 test

# GID 1000 已存在:
GID 1000 已存在

# 使用：
sudo groupadd -o -g 1000 test
```

一般不推荐。两个组同一个 GID 权限判断会混乱. 只用于特殊迁移环境。

### 05 -K 修改默认策略

```sh
# Linux 默认 GID 范围：
grep GID /etc/login.defs

# 显示:
GID_MIN 1000
GID_MAX 60000

# 创建组：
groupadd developers

# 系统自动：
GID=1000+


# 可以临时修改：
sudo groupadd \
-K GID_MIN=5000 \
developers

# 结果：
developers:x:5000:
```

## 配置模板

### 创建普通开发组

创建组:

```
# 创建:
sudo groupadd developers

# 查看：
getent group developers

# 输出：
developers:x:1001:
```

创建用户并添加组:

```sh
sudo usermod -aG developers luis

# 查看
groups luis

# 结果
luis : luis developers
```



### 创建固定 GID 的开发组

```sh
sudo groupadd \
-g 2000 \
developers

# 查看:
id luis

# 显示:
gid=2000(developers)
```

### 创建 Docker 用户组

```sh
sudo groupadd docker

# 添加用户：
sudo usermod -aG docker luis

# 重新登录：
docker ps
```

### 创建 nginx 服务组

```sh
sudo groupadd -r nginx

# 创建服务用户：
sudo useradd \
-r \
-g nginx \
-s /usr/sbin/nologin \
nginx

# 结果：
nginx 用户
    |
    └── nginx 组
```

### 脚本安全创建组

```sh
#!/bin/bash

GROUP=developers

groupadd -f $GROUP

useradd \
-m \
-s /bin/bash \
-G $GROUP \
luis
```

重复运行不会失败。
