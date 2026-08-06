# systemctl

**初始化系统** 的管理工具，用于管理：

- 系统服务（Service）
- 开机启动项
- 系统状态
- 服务日志
- Target（运行级别）
- Socket
- Timer 定时任务
- 系统电源管理

目前主流 Linux 发行版：

- Ubuntu
- Debian
- Arch Linux
- Fedora
- Rocky Linux
- AlmaLinux

## 语法

```sh
sudo systemctl start nginx
```

## 选项

| 选项                | 说明              | 示例                           |
| ----------------- | --------------- | ---------------------------- |
| `start`           | 启动服务            | `systemctl start nginx`      |
| `stop`            | 停止服务            | `systemctl stop nginx`       |
| `restart`         | 重启服务            | `systemctl restart nginx`    |
| `reload`          | 重新加载配置（不中断服务）   | `systemctl reload nginx`     |
| `status`          | 查看服务状态          | `systemctl status nginx`     |
| `enable`          | 设置开机启动          | `systemctl enable nginx`     |
| `disable`         | 禁止开机启动          | `systemctl disable nginx`    |
| `is-enabled`      | 查看是否开机启动        | `systemctl is-enabled nginx` |
| `is-active`       | 查看是否运行          | `systemctl is-active nginx`  |
| `mask`            | 禁止服务启动          | `systemctl mask nginx`       |
| `unmask`          | 解除禁止            | `systemctl unmask nginx`     |
| `list-units`      | 查看已加载 Unit      | `systemctl list-units`       |
| `list-unit-files` | 查看所有服务文件        | `systemctl list-unit-files`  |
| `daemon-reload`   | 重新加载 systemd 配置 | `systemctl daemon-reload`    |
| `show`            | 显示服务详细信息        | `systemctl show nginx`       |
| `cat`             | 查看 Unit 文件      | `systemctl cat nginx`        |
| `edit`            | 编辑服务配置          | `systemctl edit nginx`       |

## systemd 基础概念

systemctl 管理的是 **Unit**。

unit 类型：

| 类型      | 文件后缀       | 作用        |
| ------- | ---------- | --------- |
| Service | `.service` | 服务        |
| Socket  | `.socket`  | Socket 激活 |
| Target  | `.target`  | 系统运行状态    |
| Timer   | `.timer`   | 定时任务      |
| Mount   | `.mount`   | 文件挂载      |
| Path    | `.path`    | 文件路径监控    |

例如 nginx 服务: `nginx.service` ; ssh 服务: `sshd.service`

### 实例

### 01 start 启动服务

```sh
sudo systemctl start nginx

# 启动的是 nginx.service

# 查看服务状态：
systemctl status nginx

# 输出：
● nginx.service
   Loaded: loaded
   Active: active (running)
   Main PID: 1234
```

Active 解释:

| 状态               | 含义   |
| ---------------- | ---- |
| active (running) | 正常运行 |
| inactive         | 未运行  |
| failed           | 启动失败 |
| activating       | 正在启动 |


### 02 stop 停止服务

```sh
sudo systemctl stop nginx
```