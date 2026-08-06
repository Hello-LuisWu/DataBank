# usermod

用于修改已经存在的 Linux 用户账户信息。

它可以修改：
- 用户名
- 用户 UID
- 主组
- 附加组
- 家目录
- 登录 Shell
- 密码策略
- 用户有效期
- 用户锁定状态

## 语法

```sh
usermod [选项] [用户名]
```

---

## 选项

| 选项   | 说明               | 示例                              |
| ---- | ---------------- | ------------------------------- |
| `-a` | 追加附加组,要和 -G 一起使用 | `usermod -aG docker luis`       |
| `-c` | 修改账号备注           | `usermod -c "Developer" luis`   |
| `-d` | 修改登录目录           | `usermod -d /data/luis luis`    |
| `-e` | 设置账号过期日期         | `usermod -e 2026-12-31 luis`    |
| `-f` | 设置密码过期后禁用天数      | `usermod -f 30 luis`            |
| `-g` | 修改用户所属组          | `usermod -g developers luis`    |
| `-G` | 修改用户所属附加组        | `usermod -G docker,sudo luis`   |
| `-l` | 修改账户名称           | `usermod -l newname oldname`    |
| `-m` | 移动用户家目录（配合 -d）   | `usermod -d /data/luis -m luis` |
| `-r` | 删除附加组            | `usermod -rG docker luis`       |
| `-s` | 修改用户默认 shell     | `usermod -s /bin/zsh luis`      |
| `-u` | 修改用户 UID         | `usermod -u 2000 luis`          |
| `-L` | 锁定用户密码。使密码无效     | `usermod -L luis`               |
| `-U` | 解除密码锁定           | `usermod -U luis`               |

---

## 实例

### 00. -aG 添加附加组（最常用）

```sh
# 再追加一个 docker 组
sudo usermod -aG docker luis

# 查看:
groups luis

# 结果:
luis : luis docker
```

### 01. -d 更改登录目录

```sh
sudo usermod -d /data/luis luis
```

### 01.1 -d + -m 移动家目录

```sh
sudo usermod \
-d /data/luis \
-m \
luis
```

旧：`/home/luis`

移动：`/data/luis`

同时修改：`/etc/passwd`

### 02. -u 改变用户的uid

```sh
usermod -u 2000 luis
```

### 03 -G 修改附加组

```sh
sudo usermod -G sudo,docker luis

# 结果:
luis
 |
 +-- sudo
 +-- docker
```

注: `-aG(add Group)` 是追加. `-G` 是覆盖原来的附加组.

```sh
# 原来:
luis : developers docker

# 执行:
usermod -G sudo luis

# 结果:
luis : sudo
```

### 04 -g 修改主组

```sh
sudo usermod -g developers luis

# 查看:
id luis

# 输出:
uid=1001(luis)
gid=2000(developers)
```

区别：`-g` 主组. `-G` 附加组

### 05 -s 修改 Shell

```sh
sudo usermod -s /bin/zsh luis

# 查看:
grep luis /etc/passwd

# 输出:
luis:x:1001:1001::/home/luis:/bin/zsh
```

### 06 -l 修改用户名

```sh
usermod -l 新用户名 旧用户名
```

注意,不会自动修改：`/home/luis`

**修改目录:**

```sh
sudo usermod -d /home/新用户名 -m 新用户名
```

### 07 -u 修改 UID

```sh
sudo usermod -u 2000 luis

# 查看:
id luis

# 显示：
uid=2000(luis)
```

注意: 修改 UID 后旧文件仍属于就的 uid

需要:

```sh
sudo find / -user 1001 -exec chown -h luis {} \;
```

### 08 -L 锁定用户

```sh
sudo usermod -L luis
```

密码前面新增 `!` 符号

```sh
# 查看：
sudo grep luis /etc/shadow
```

### 09 -U 解锁用户

```sh
sudo usermod -U luis
```

恢复登录.

### 10 -e 设置账号过期日期

```sh
sudo usermod -e 2026-12-31 luis
```

表示 2026-12-31 后账号失效

用 `chage -l luis` 命令查看.

### 11 -f 设置密码过期后禁用时间

```sh
sudo usermod -f 30 luis
```

表示30天后禁用账号

---

注意: arch 添加组需要修改 `sudo visudo`

