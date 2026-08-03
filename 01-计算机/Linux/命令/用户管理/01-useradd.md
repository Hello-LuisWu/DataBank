# useradd

建立用户帐号，用 passwd 设定帐号的密码。而可用 userdel 删除帐号。实际上是保存在 /etc/passwd 文本文件中。

| options         | info                                               |
|-----------------|----------------------------------------------------|
| `-m`            | 为用户创建家目录,会创建 `/home/用户名`             |
| `-c "xxxxx"`    | 加上备注文字。备注文字会保存在passwd的备注栏位中。 |
| `-d /xxx/xxx`   | 指定用户家目录(可以指定任意路径)                   |
| `-D`            | 查看/变更预设值                                    |
| `-s /bin/xxsh`  | 指定默认 shell                                     |
| `-g xxx`        | 为用户添加组                                       |
| `-G xxx,xxx`    | 为用户添加附加组                                   |
| `-M`            | 不要自动建立用户的登入目录。                       |
| `-e 2026-12-31` | 设置账号过期时间                                  |
| `-f 7`          | 指定在密码过期后多少天即关闭该帐号                 |
| `-r`            | 建立系统帐号                                       |
| `-u 2000`       | 指定用户 uid                                       |
|

**01. -m 创建家目录:**

```sh
sudo useradd -m luis
```

**02. -d 指定用户家目录:**

```sh
sudo useradd -m -d /data/luis luis

# 结果:
# /data/luis

# 而不是:
# /home/luis

```

**03. -g 为用户添加组(不指定组名,默认会创建和用户名一样的组):**

```sh
useradd -m -g global luis

# 表示:

#    用户:
#    luis
#
#    主组:
#    global
#    
```

**04. -G 添加附加组:**

```sh
sudo useradd -m -G wheel,docker luis

# 表示:
#    主组:
#    luis
#
#    附加组:
#    wheel
#    Docker
```

**05. -s 指定登录 Shell:** 

```sh
sudo useradd -m -s /bin/zsh luis

# 查看 shell
echo $SHELL

# 结果:
/bin/zsh
```

**06. -c 添加用户备注:**

```sh
sudo useradd -m -c "Developer Account" luis
```

**07. -u 指定 UID:**

```sh
sudo useradd -m -u 2000 luis
```

**08. -r 创建系统用户:**

```sh
sudo useradd -r nginx
```

特点：
- UID 通常 < 1000
- 不创建 home
- 用于服务程序,例如:nginx mysql redis 等

**09. -e 设置账号过期时间:**

```sh
sudo useradd -e 2026-12-31 test

# 账户在 2026-12-31 后失效

# 查看
chage -l test
```

**10. -f 密码过期后多少天禁用:**

```sh
# 格式:
-f 天数

sudo useradd -f 7 luis
```

密码过期后7天后禁用账号!

**11. -p 设置密码（不推荐）:**

```sh
sudo useradd -p '$6$xxx' luis
```

推荐 `passwd luis`

**12. -D 查看 useradd 默认配置:**

```sh
useradd -D
```

修改默认 shell:

```sh
sudo useradd -D -s /bin/zsh

useradd -m test
```

## 账户创建模版

**0. 创建普通开发用户（推荐）:**

```sh
sudo useradd \
-m \
-s /bin/zsh \
-G sudo \
-c "Developer" \
luis

sudo passwd luis
```


**1. 个人配置用户:**

```sh
sudo useradd -m -s /bin/zsh -G wheel luis
sudo passwd luis
```

**2. 创建 Ubuntu用户:**

```sh
sudo useradd -m -s /bin/bash -G sudo luis
sudo passwd luis
```

**3. 创建 Arch Linux 管理员用户:**

```sh
sudo useradd \
-m \
-s /bin/zsh \
-G wheel \
luis

sudo passwd luis

# 启用 wheel:
sudo visudo
```

**取消注释:**

```
将:
# %wheel ALL=(ALL:ALL) ALL

改:
%wheel ALL=(ALL:ALL) ALL
```

**4. 服务器服务账号：**

```sh
sudo useradd -r -s /usr/sbin/nologin service
```

**4. 创建 Web 服务账号:**

```sh
sudo useradd \
-r \
-s /usr/sbin/nologin \
-d /var/www \
nginx

# 结果:
# nginx
#  |
#  +-- 无法登录
#  +-- 服务专用
#  +-- home=/var/www
```

**6. 创建 Docker 用户:**

```sh
sudo useradd \
-m \
-s /bin/bash \
-G docker \
docker
```

## adduser 和 useradd 区别

| xx           | useradd    | adduser  |
|--------------|------------|----------|
| 类型         | 底层命令   | 脚本封装 |
| 自动创建home | ❌ 默认无  | ✅       |
| 交互提示     | ❌         | ✅       |
| 适合脚本     | ⭐⭐⭐⭐⭐ | ⭐⭐     |
| 新手         | 一般       | 推荐     |
| 服务器       | 推荐       | 可用     |

常用 Arch / Ubuntu Server / WSL，建议掌握：
useradd + groupadd + usermo d + passwd + visudo 这套用户管理组合。
