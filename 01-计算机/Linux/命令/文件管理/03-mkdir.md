# mkdir

make directory, 创建目录

## 语法

```sh
mkdir [选项] [path/目录名]
```

---

## 选项

| 选项   | 说明             | 示例                  |
| ---- | -------------- | ------------------- |
| `-p` | 递归操作, 同时创建多级目录 | `mkdir -p a/b/c`    |
| `-m` | 创建目录时设置权限      | `mkdir -m 755 test` |


---

## 实例

### 01 创建目录

```sh
mkdir dir1
```

### 02 创建多个目录

```sh
mkdir dir1 dir2 dir3
```

### 03 创建多级目录

```sh
mkdir -p dir/a/b/c

# 会自动创建：
a/
└── b/
    └── c/
```

### 04 使用大括号批量创建目录

```sh
mkdir -p project/{src,docs,test}

# 生成：
project/
├── src/
├── docs/
└── test/
```

### 05 创建目录时指定权限

```sh
mkdir -m 755 website

# 创建私有目录
mkdir -m 700 private

# 创建共享目录
mkdir -m 777 share
```

### 06 创建网站目录结构

```sh
mkdir -p website/{css,js,img}

# 生成：
website/
├── css/
├── js/
└── img/
```
