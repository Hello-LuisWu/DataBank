# chsh 

change shell 更改默认shell

选项:

| 选项  | 说明           |
| --- | ------------ |
| s   | 改变当前的shell设置 |

## 实例

把 zsh 设置为默认 shell

```sh
chsh -s /bin/zsh

# 或者
chsh -s $(which zsh)
```

重启生效