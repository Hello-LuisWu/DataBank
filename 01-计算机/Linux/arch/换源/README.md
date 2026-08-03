
# arch-initconfig

### 换国内镜像源

编辑 `/etc/pacman.d/mirrorlist` 文件, 在顶部添加以下内容

```sh
#清华源
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
#阿里源
Server = http://mirrors.aliyun.com/archlinux/$repo/os/$arch
#中科大源
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
```

`sudo pacman -Syyu` 更新源 缓存

**或者运行以下命令更换:**

```sh
# 查看能用的源镜像排名，并选择速度较快的一个或多个源
sudo pacman-mirrors -i -c China -m rank

查看选择的源
sudo vim /etc/pacman.d/mirrorlist

# 更新源
sudo pacman -Syyu
```

### 添加非官方 的源

用 `vim /etc/pacman.conf` 命令打开 `pacman.conf` 文件，或者也可以写在`/etc/pacman.d/mirrorlist` 文件里

> 注：pacman.conf 是 pacman 的总主控配置；mirrorlist 只是单独存放「所有镜像服务器地址」的引用文件，被 pacman.conf 导入使用。
