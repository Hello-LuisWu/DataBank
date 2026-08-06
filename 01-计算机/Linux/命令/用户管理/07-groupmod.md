# groupmod

用于修改已经存在的 Linux 用户组信息。

它可以修改：
- 用户组名称
- GID（Group ID）
- 其他组属性

常用于：

- 修改项目组名称
- 统一服务器 GID
- 迁移文件权限
- 调整用户组管理策略

## 语法

```sh
groupmod [参数] 组名
```

## 选项

| 选项 | 说明                          | 示例                             |
|------|-------------------------------|----------------------------------|
| `-g` | 修改组 GID                    | `groupmod -g 2000 developers`    |
| `-n` | 修改组名称                    | `groupmod -n dev developers`     |
| `-o` | 允许使用重复 GID              | `groupmod -o -g 1000 developers` |
| `-R` | 指定替代根目录（chroot 环境） | `groupmod -R /mnt group`         |
| `-h` | 显示帮助信息                  | `groupmod -h`                    |

## 实例

### 01 -n 修改用户组名称（最常用）

```sh
groupmod -n 新组名 原组名
```

### 02 -g 修改 GID

```sh
 groupmod -g 新GID 组名
```

### 03 -o 允许重复 GID

```sh
sudo groupmod -o -g 1000 developers
```

### 04 -R 指定替代根目录

```sh
sudo groupmod -R /mnt/server developers
```
