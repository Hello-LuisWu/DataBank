# Shell Expansion（Shell 展开）

## 选项

| 类型                   | 符号       | 名称    | 作用        | 示例         |
| -------------------- | -------- | ----- | --------- | ---------- |
| Brace Expansion      | `{}`     | 花括号展开 | 生成字符串列表   | `{a,b,c}`  |
| Brace Range          | `{..}`   | 序列展开  | 生成数字/字母序列 | `{1..10}`  |
| Tilde Expansion      | `~`      | 家目录展开 | 用户目录      | `~/file`   |
| Parameter Expansion  | `$变量`    | 参数展开  | 获取变量值     | `$HOME`    |
| Command Substitution | `$()`    | 命令替换  | 使用命令输出    | `$(date)`  |
| Arithmetic Expansion | `$(( ))` | 算术展开  | 数学计算      | `$((1+2))` |
| Filename Expansion   | `* ? []` | 文件名展开 | 匹配文件      | `*.txt`    |
| Quote Expansion      | `' '`    | 单引号   | 禁止展开      | `'$HOME'`  |
| Quote Expansion      | `" "`    | 双引号   | 保留变量展开    | `"$HOME"`  |
| Escape               | `\`      | 转义    | 禁止特殊含义    | `\$USER`   |
|                      |          |       |           |            |

## 实例

### 1 花括号展开 Brace Expansion `{}`

详情请参考：[01-命令中括号的使用技巧详解](https://github.com/Hello-LuisWu/DataBank/blob/main/01-%E8%AE%A1%E7%AE%97%E6%9C%BA/Linux/%E5%91%BD%E4%BB%A4/%E7%AC%A6%E5%8F%B7%E5%BA%94%E7%94%A8/01-%E5%91%BD%E4%BB%A4%E4%B8%AD%E6%8B%AC%E5%8F%B7%E7%9A%84%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E8%AF%A6%E8%A7%A3.md)

### 2 序列展开 Brace Range `{..}`

详情请参考：[01-命令中括号的使用技巧详解](https://github.com/Hello-LuisWu/DataBank/blob/main/01-%E8%AE%A1%E7%AE%97%E6%9C%BA/Linux/%E5%91%BD%E4%BB%A4/%E7%AC%A6%E5%8F%B7%E5%BA%94%E7%94%A8/01-%E5%91%BD%E4%BB%A4%E4%B8%AD%E6%8B%AC%E5%8F%B7%E7%9A%84%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E8%AF%A6%E8%A7%A3.md)

### 3 家目录展开 Tilde Expansion `~`

用于展开用户目录。

```sh
echo ~

# 结果:
/home/luis
```

进入家目录

```sh
cd ~

# 等价：
cd $HOME

# 进入指定用户目录：
cd ~root

# 展开：
/root
```

### 4 变量展开 Parameter Expansion `$xxx`

变量展开。

```sh
# 普通变量：
name="Linux"

echo $name

# 输出：
Linux
```

详情请查看：[01-Linux 环境变量详解](https://github.com/Hello-LuisWu/DataBank/blob/main/01-%E8%AE%A1%E7%AE%97%E6%9C%BA/Linux/%E5%91%BD%E4%BB%A4/%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE/01-Linux%20%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E8%AF%A6%E8%A7%A3.md)

### 5 命令替换 Command Substitution `$()`

详情请查看：[01-命令中括号的使用技巧详解](https://github.com/Hello-LuisWu/DataBank/blob/main/01-%E8%AE%A1%E7%AE%97%E6%9C%BA/Linux/%E5%91%BD%E4%BB%A4/%E7%AC%A6%E5%8F%B7%E5%BA%94%E7%94%A8/01-%E5%91%BD%E4%BB%A4%E4%B8%AD%E6%8B%AC%E5%8F%B7%E7%9A%84%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E8%AF%A6%E8%A7%A3.md)

### 6 算术展开 Arithmetic Expansion `$(( ))`

详情请查看：[[01-命令中括号的使用技巧详解]]

### 7 文件名通配符展开 Filename Expansion `* ? []`

详情请查看：[03-Linux 通配符详解（Wildcard）](https://github.com/Hello-LuisWu/DataBank/blob/main/01-%E8%AE%A1%E7%AE%97%E6%9C%BA/Linux/%E5%91%BD%E4%BB%A4/%E7%AC%A6%E5%8F%B7%E5%BA%94%E7%94%A8/03-Linux%20%E9%80%9A%E9%85%8D%E7%AC%A6%E8%AF%A6%E8%A7%A3%EF%BC%88Wildcard%EF%BC%89.md)

### 8 单引号 Quote Expansion `'`

单引号 `'` 完全禁止展开：

```sh
echo '$HOME'

# 打印：
$HOME

# 而不是输出变量值
/home/luis
```

用单引号 `'` 引用变量，内部所有字符都按字面意思处理。

详情请查看：[01-Linux 环境变量详解](https://github.com/Hello-LuisWu/DataBank/blob/main/01-%E8%AE%A1%E7%AE%97%E6%9C%BA/Linux/%E5%91%BD%E4%BB%A4/%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE/01-Linux%20%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E8%AF%A6%E8%A7%A3.md)

### 9 双引号 Quote Expansion `"`

允许变量展开、命令替换

```sh
echo "$HOME"

# 变量展开：
/home/luis
```

用双引号 `"` 引用变量，允许解析内部的变量和命令

详情请查看：[01-Linux 环境变量详解|Linux 环境变量详解](https://github.com/Hello-LuisWu/DataBank/blob/main/01-%E8%AE%A1%E7%AE%97%E6%9C%BA/Linux/%E5%91%BD%E4%BB%A4/%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE/01-Linux%20%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E8%AF%A6%E8%A7%A3.md)

### 转义字符 Escape `\` 

```sh
echo \$HOME

# 输出：
$HOME
```

