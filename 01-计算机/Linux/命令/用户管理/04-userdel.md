# userdel

用于删除 Linux 用户账户。

它可以删除：

- 用户账号
- 用户所属信息
- 用户家目录
- 用户邮件文件
- 用户相关文件

## 语法

```sh
userdel [-r] [用户帐号]
```

---

## 选项

| 选项   | 说明                     | 案例                     |
| ---- | ---------------------- | ---------------------- |
| `-f` | 强制删除用户，即使用户正在登录或存在异常情况 | `userdel -f luis`      |
| `-r` | 删除用户同时删除家目录和邮件文件       | `userdel -r luis`      |
| `-R` | 指定替代根目录（chroot 环境）     | `userdel -R /mnt user` |
| `-Z` | 删除用户对应的 SELinux 映射     | `userdel -Z luis`      |

---

## 实例

### 1 -r 删除用户及家目录（最常用）

```sh
userdel -r hnlinux

# 将删除：
用户:
luis

家目录:
 /home/luis

邮件:
 /var/mail/luis
```

不使用 `-r`:

```sh
sudo userdel luis

# 只删除:
/etc/passwd
/etc/shadow
/etc/group
```

但是 `/home/luis` 会保留


**为什么默认不删除 home？**

因为用户可能：
- 有重要数据
- 有项目文件
- 需要恢复账户

### 2 -f 强制删除用户

```sh
sudo userdel -f luis
```

作用：

即使
- 用户正在登录
- 用户进程运行
- 用户文件异常

都会被删除

### 3 -Z 删除 SELinux 用户映射

```sh
sudo userdel -Z apache

# 将删除:
Linux 用户
    |
    |
SELinux 用户映射
```

适用于：

- Fedora
- RHEL
- CentOS
- Rocky Linux

如果没有 SELinux：通常不用。

### 4 -R 指定替代根目录

```sh
sudo userdel -R /mnt/server luis
```

表示不要操作当前系统 `/`, 而是操作 `mnt` 里面的用户数据库。

使用场景：
- 系统修复
- chroot
- 救援模式

普通用户基本不用。

## 用户删除 & 检查

### 查看用户信息:

```sh
id luis

# 结果:
uid=1001(luis)
gid=1001(luis)
groups=1001(luis),10(wheel)
```

### 查看用户文件:

```sh
sudo find / -user luis

# 结果:
/home/luis/project
/var/www/test
```

### 删除普通用户（保留数据）:

```sh
sudo userdel luis
```

将删除:

- `/etc/passwd`
- `/etc/shadow`

将保留:

- `/home/luis`

适合：

- 临时禁用账号
- 保留用户文件

### 彻底删除用户

```sh
sudo userdel -r luis
```

将删除:

- 用户账号
- 密码信息
- home目录
- 邮件文件

适合：
- 测试用户
- 废弃账号

### 删除服务用户

```sh
sudo userdel nginx

# 如果有 web 数据:
sudo rm -rf /var/www
```

### 批量删除测试用户

例如：
- test1
- test2
- test3

```sh
sudo userdel -r test1
sudo userdel -r test2
sudo userdel -r test3
```


