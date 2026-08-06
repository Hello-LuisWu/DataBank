# passwd

passwd 是 Linux 中用于管理用户密码的命令，同时可以管理：

- 修改用户密码
- 设置密码过期策略
- 锁定 / 解锁用户
- 查看密码状态
- 强制用户下次登录修改密码

密码信息保存在 `/etc/shadow`

---

## 格式

```sh
passwd [参数] 用户名
```

---

## 参数

| 选项      | 说明                                 | 实例                                          |
|-----------|--------------------------------------|-----------------------------------------------|
| `-a`      | 与 `-S` 配合，显示所有用户密码状态   | `passwd -Sa`                                  |
| `-d`      | 删除用户密码（取消密码）             | `passwd -d luis`                              |
| `-e`      | 立即使密码过期，强制下次登录修改     | `passwd -e luis`                              |
| `-i`      | 设置密码过期后多少天禁用账号         | `passwd -i 30 luis`                           |
| `-l`      | 锁定用户密码                         | `passwd -l luis`                              |
| `-n`      | 设置密码最短修改间隔                 | `passwd -n 7 luis`                            |
| `-R`      | 指定根目录（用于 chroot 环境）       | `passwd -R /mnt luis`                         |
| `-S`      | 查看密码状态                         | `passwd -S luis`                              |
| `-u`      | 解锁用户密码                         | `passwd -u luis`                              |
| `-w`      | 设置密码过期提醒时间                 | `passwd -w 7 luis`                            |
| `-x`      | 设置密码最大有效期                   | `passwd -x 90 luis`                           |
| `--stdin` | 从标准输入读取密码（部分发行版支持） | `echo pass \| passwd --stdin user` |

---

## 实例

### 01 修改用户密码

**修改当前用户密码:** 

```sh
passwd

# 提示:
Current password:
New password:
Retype new password:
```

**root 修改其他用户密码:**

```sh
sudo passwd luis

# 提示:
New password:
Retype new password:
passwd: password updated successfully
```

### 2 -S 查看密码状态

```sh
passwd -S luis

# 输出:
luis PS 2026-08-03 0 99999 7 -1
```

**输出含义:**

| 字段       | 含义         |
|------------|--------------|
| luis       | 用户名       |
| PS         | 密码状态     |
| 2026-08-03 | 最近修改时期 |
| 0          | 最短修改间隔 |
| 99999      | 最大有效期   |
| 7          | 提前提醒天数 |

**密码状态:**

| 状态 | 含义       |
|------|------------|
| PS   | 已设置密码 |
| NP   | 无密码     |
| LK   | 密码锁定   |

**查看所有用户:**

```sh
sudo passwd -Sa

# 例如:
root PS
luis PS
mysql LK
```

### 03 -l 锁定用户

```sh
sudo passwd -l luis

# 显示:
passwd: password changed.

# 查看:
passwd -S luis

# 显示:
luis LK
```

注意:
锁定密码时禁止密码登录. 

但：
SSH Key 登录可能仍然有效
root 可以解锁


### 04 -u 解锁用户

```sh
sudo passwd -u luis

# 查看:
passwd -S luis

# 显示:
luis PS
```

### 05 -d 删除密码

```sh
sudo passwd -d test

# 结果:
passwd: password updated successfully

# 查看:
passwd -S test

# 显示:
test NP
```

表示无密码

### 06 -e 强制密码过期

```sh
sudo passwd -e luis

# 用户下一次登录：
You are required to change your password
```

必须修改密码。

**应用场景:**

```sh
useradd -m tom
passwd tom
passwd -e tom
```

第一次登录强制改密码。

### 07 -x 设置密码最大有效期

```sh
sudo passwd -x 90 luis
```

表示密码 90 天后过期

**查看密码状态:** 

```sh
passwd -S luis

# 显示:
90
```

适用于公司服务器, 密码90天必须更换

### 08 -n 设置密码最短修改时间

```sh
sudo passwd -n 7 luis
```

用户修改密码后7天内不能再次修改, 防止连续修改密码绕过历史限制

### 09 -w 设置过期提醒时间

```sh
sudo passwd -w 14 luis
```

密码过期前: 14天开始提醒


### 10 -i 设置密码过期后禁用时间

```sh
sudo passwd -i 30 luis
```

密码过期后：

30天内不修改
↓
账号禁用

## 实际应用实例

### 01 创建用户并设置密码

```sh
sudo passwd luis
```

### 02 创建管理员账户

ubuntu:

```sh
sudo useradd \
-m \
-s /bin/bash \
-G sudo \
luis

sudo passwd luis
```

### 03 强制新用户第一次登录修改密码

```sh
# 创建:
sudo useradd -m developer

# 设置密码：
sudo passwd developer

# 第一次强制修改:
sudo passwd -e developer
```

### 04 锁定离职员工账号

```sh
sudo passwd -l tom

# 查看:
passwd -S tom

# 结果:
tom LK
```

### 05 服务器密码策略

要求：
- 90天必须修改密码
- 提前14天提醒
- 过期30天禁用

```sh
sudo passwd \
-x 90 \
-w 14 \
-i 30 \
luis

# 查看:
passwd -S luis
```

### 06 批量查看用户密码状态

```sh
sudo passwd -Sa

# 输出:
root PS
luis PS
nginx LK
mysql LK
```


## 服务器常用组合

创建普通用户:

```sh
useradd -m -s /bin/bash luis
passwd luis
```

创建服务用户:

```sh
useradd \
-r \
-s /usr/sbin/nologin \
nginx

passwd -l nginx
```

设置安全密码策略:

```sh
passwd \
-x 90 \
-w 14 \
-i 30 \
luis
```

## 常用记忆

```sh
# 修改密码
passwd user

# 查看状态
passwd -S user

# 锁定
passwd -l user

# 解锁
passwd -u user

# 强制修改密码
passwd -e user

# 密码90天过期
passwd -x 90 user
```

Linux 用户管理中常见组合：

```sh
useradd
   ↓
passwd
   ↓
usermod
   ↓
chmod/chown
```
