# 🛜 Netplan 配置

Netplan 是 Ubuntu 从 17.10 版本开始引入的**默认网络配置工具**，并在 Ubuntu 18.04 及更高版本中成为管理网络接口的标准方式。

它的核心定位是 "网络配置抽象渲染器"（network configuration abstraction renderer）—— 简单来说，你只需要用一份 YAML 格式的配置文件描述"网络想要什么样"，Netplan 就会自动将其转换成底层后端工具能理解的格式并应用

## 一、常用网络查看命令

| 命令                  | 作用                                       |
| ------------------- | ---------------------------------------- |
| `ip addr`           | 查看 ip、网卡名称                               |
| `ip route`          | 查看路由                                     |
| `nmcli`             | NetworkManager 管理                        |
| `ping`              | 测试网络                                     |
| `ss -tulnp`         | 查看端口                                     |
| `resolvectl status` | 查看 DNS                                   |
| `hostname -I`       | 快速查看 IP                                  |
| `ifconfig`          | 查看 ip ；用 `sudo apt install net-tools` 安装 |

## 二、netplan 工作原理

netplan 只是生成一份网络配置文件。netplan 本身并不会直接操作网络。而是让其他服务（程序）调用这份文件进行网络配置！

### 1 完整流程

```sh
	 用户配置
	    ↓
        ↓
     netplan
        |
        | 生成配置
        |
        ↓
+----------------------+
| systemd-networkd     |
| 或 NetworkManager   |
+----------------------+
        |
        |
        ↓
    Linux Kernel
        |
        |
        ↓
    网卡设备
```

1. 读取 `/etc/netplan/*.yaml` 用户配置
2. 生成临时后端配置存放于 `/run`
3. 交给后端守护进程 (networkd / NetworkManager) 控制网卡

### 2 配置文件目录

Netplan 从三个目录读取配置，按优先级排列：

| 目录              | 说明                | 优先级    |
| --------------- | ----------------- | ------ |
| `/run/netplan/` | 运行时配置（临时生效）       | **最高** |
| `/etc/netplan/` | 管理员配置（**你修改的地方**） | 中等     |
| `/lib/netplan/` | 系统默认配置            | 最低     |

### 3 配置文件名

文件常见面：

- `00-installer-config.yaml` — Ubuntu Server 安装程序生成
- `01-netcfg.yaml` — 通用配置文件名
- `01-network-manager-all.yaml` — Ubuntu Desktop（交给 NetworkManager）
- `50-cloud-init.yaml` — 云服务器（AWS、Azure 等）

文件名前面的数字越小优先级越高

## 三、参数结构

Ubuntu 用 YAML 作为网络配置文件。YAML 用缩进表示层级，**结构清晰易维护，且能简洁地声明复杂的网络依赖关系**，替代了传统配置文件晦涩的命令式脚本。

需严格遵循 YAML 语法规则：

- 缩进只能用空格！一般每层 2 空格！
- 严禁使用 Tab。
- `key:`冒号**后面必须带空格**
- IP 地址格式必须带掩码 `/24`，不能单独写子网掩码
- yaml 严格大小写敏感


```sh
network:                       
  version: 2                   
  renderer: networkd           
  ethernets: ...                
  wifis: ...                    
  bonds: ...                    
  bridges: ...                  
  vlans: ...                    
  tunnels: ...                  
  # ... 更多设备类型
```

### 1 network

 根节点。所有网络配置都必须包裹在这个键下面。

```sh
network:
  ...
  ...
```

### 2 version

指定 Netplan 配置的**语法版本**。目前必须是 `2` ， `1` 已废弃。如果不写或写错会直接报错。

```sh
network:
  version: 2
```

### 3 renderer

renderer 决定谁管理网络。指定由**哪个后端服务**（程序）来执行最终的网络配置。Netplan 本身只负责生成配置，不负责执行。

- `networkd`：Ubuntu 自带的系统网络管理服务（`systemd-networkd`）。**轻量、稳定**，适合**服务器**环境。
	- 轻量
	- 启动快
	- 适合云服务器
	- Ubuntu Server 默认
- `NetworkManager`：功能更丰富（支持动态切换、图形界面）。适合**桌面版** Ubuntu。
	- 支持 WiFi
	- 支持 VPN
	- 图形界面管理

```sh
# systemd-networkd
network:
  version: 2
  renderer: networkd

# NetworkManager
network:
  version: 2
  renderer: NetworkManager

```

### 4 ethernets（以太网）

配置**有线以太网卡**（如 `eth0`、`ens33`、`enp0s3`）。这是最常用的配置块，用于设置办公网络、服务器外网/内网 IP 等。

```sh
ethernets:
  ens160:
    dhcp4: true    
```

对应 物理网卡 ---> ens160

包含的参数：

| 参数          | 作用        |
| ----------- | --------- |
| dhcp4       | IPv4 自动获取 |
| dhcp6       | IPv6 自动获取 |
| addresses   | 静态 IP     |
| routes      | 路由        |
| nameservers | DNS       |
| mtu         | 最大传输单元    |
| macaddress  | 指定 MAC    |
| optional    | 是否等待设备启动  |

#### 4.1 配置动态 ip

```sh
network:
  version: 2
  renderer: networkd
  ethernets:
    ens160:
      dhcp4: true # tree：自动获取；false：手动添加
      dhcp6: true # 自动获取 ipv6
```

#### 4.2 配置静态 ip

查看网卡名称：

```sh
ip addr

# 输出：
2: ens160:
    inet 192.168.10.20
```

其中 `ens160` 就是网卡名称

```sh
network:
  version: 2 
  renderer: networkd # 二选一 networkd / NetworkManager
  ethernets:
    ens160: # 替换为你的网卡名称
      dhcp4: false # 关闭DHCP
      dhcp6: false # 建议关闭，VMware NAT 通常不提供 IPv6，可能导致网络等待或异常。
      addresses: # 设置你的静态IP和子网掩码 
        - 192.168.28.135/24 # /24：子网掩码 255.255.255.0
      routes: # 默认网关
        - to: default
          via: 192.168.28.2 # 设置默认网关 使用 ip route 命令查看
      nameservers:
        addresses:
          - 223.5.5.5 # 配置 DNS 服务器
          - 119.29.29.29
```

用 `sudo netplan apply` 应用配置

#### 4.3 子网掩码

子网掩码决定一个 IP 地址属于哪个网络，以及这个网络里面可以有多少台设备。

现在 Linux 更常用 CIDR 表示法来划分子网掩码。常见 CIDR 对照表：

| CIDR | 子网掩码            | 网络位 | 主机位 | 可用主机数量   |
| ---- | --------------- | --- | --- | -------- |
| /8   | 255.0.0.0       | 8   | 24  | 16777214 |
| /16  | 255.255.0.0     | 16  | 16  | 65534    |
| /24  | 255.255.255.0   | 24  | 8   | 254      |
| /25  | 255.255.255.128 | 25  | 7   | 126      |
| /26  | 255.255.255.192 | 26  | 6   | 62       |
| /27  | 255.255.255.224 | 27  | 5   | 30       |
| /28  | 255.255.255.240 | 28  | 4   | 14       |
| /29  | 255.255.255.248 | 29  | 3   | 6        |
| /30  | 255.255.255.252 | 30  | 2   | 2        |

#### 4.4 通过 MAC 地址精确匹配网卡

```sh
network:
  version: 2
  renderer: networkd
  ethernets:
    ens160:
      dhcp4: true # tree：自动获取；false：手动添加
      dhcp6: true # 自动获取 ipv6
      match:
        macaddress: 00:0c:29:c2:47:29
      set-name: ens160
```

这种方式可以避免网卡名称漂移（比如 `eth0` 突然变成 `eth1`）

### 5 wifis（无线网络）

配置**无线网卡**（如 `wlan0`、`wlp2s0`）。它比 `ethernets` 多了一个必须的子键 **`access-points:`**，用来指定要连接的 Wi-Fi 名称（SSID）和密码（如 WPA2-PSK 或企业级 802.1X 认证）。

```
wifis:
  wlan0:
    dhcp4: true
    access-points:
      "MyWiFi":
        password: "12345678"
```

结构：

```
无线网卡

wlan0
  |
  |
WiFi路由器
```

包含参数：

| 参数            | 作用     |
| ------------- | ------ |
| access-points | WiFi列表 |
| password      | 密码     |
| dhcp4         | 自动获取IP |
| addresses     | 静态IP   |

#### 5.1 配置动态 ip：

```sh
network:
  version: 2
  renderer: networkd
  wifis:
    wlan0:                            # 你的无线网卡名称，需要替换
      access-points:
        "你的SSID":
          password: "你的密码"          # [reference:32]
      dhcp4: true
```

#### 5.2 配置静态 ip：

配置方法与 `ethernets` 一致

```sh
network:
  version: 2
  renderer: networkd
  wifis:
    wlan0:                            # 你的无线网卡名称，需要替换
      access-points:
        "Wifi-Name":
          password: "Wifi-Password"
      dhcp4: false
      addresses:
		- 192.168.1.100/24
	  routes:
		- to: default
		  via: 192.168.1.1
	  nameservers:
	    addresses: 
          - 8.8.8.8
	      - 114.114.114.114
```

#### 5.3 配置多个 WiFi：

```sh
network:
  version: 2
  renderer: networkd
  wifis:
    wlan0:    
      dhcp4: true                        # 你的无线网卡名称，需要替换
      access-points:
        "House-Wifi":
          password: "Wifi-Password"          # [reference:32]
        "Phone-Wifi":
          password: "Wifi-Password"
```

#### 5.4指定 Wi-Fi 频段：

```sh
network:
  version: 2
  renderer: networkd
  wifis:
    wlan0:    
      dhcp4: true                        # 你的无线网卡名称，需要替换
      access-points:
        "House-Wifi":
          password: "Wifi-Password"          # [reference:32]
          band: 5GHz # 或 2.4GHz
```

### 6 Netplan 命令

#### 6.1 检查配置

```sh
sudo netplan generate
```

检查 YAML 文件并生成配置

#### 6.2 立即生效配置

```sh
sudo netplan apply
```

保存文件后，运行以下命令使配置生效

#### 6.3 安全测试

```sh
sudo netplan try
```

会等待 60-90 秒，期间按 `enter` 应用配置，无操作则恢复之前配置

流程：

```
临时应用
   |
等待确认
   |
没确认
   |
自动恢复
```

期间会显示：

```
Do you want to keep these settings?
```

按 `enter` 即可保存配置