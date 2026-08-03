
# cargo-config

## 介绍

‌cargo 是 Rust 官方的构建工具 + 包管理器，类似于 python 的pip 和 nodejs 的 npm

Cargo 可以：

- 创建 Rust 项目
- 管理依赖库（crate）
- 编译程序
- 运行测试
- 发布 Rust 包
- 安装 Rust 命令行工具

可通过 rustup 安装 Rust，安装 Rust 时会同时也会安装 Cargo。详情请参考：[rustup](https://rustup.rs/) & [Install Rust and Cargo](https://doc.rust-lang.org/stable/cargo/getting-started/installation.html)


## 安装 cargo and rust

 Unix/Linux:

 ```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

## 或者安装 cargo-binstall  Rust 二进制安装器


[cargo-binstall](https://github.com/cargo-bins/cargo-binstall) 作用是优先下载别人已经编译好的 Rust 二进制文件，而不是本地重新编译源码；适合只想用 Rust 编译好的 CLI（Command Line Interface Tool）命令行工具，而不需要完整的 Rust 开发环境。

**Unix/Linux:**

```
curl -L --proto '=https' --tlsv1.2 -sSf https://raw.githubusercontent.com/cargo-bins/cargo-binstall/main/install-from-binstall-release.sh | bash
```

**homebrew:**

```
brew install cargo-binstall
```

**windows:**

```
Set-ExecutionPolicy Unrestricted -Scope Process; iex (iwr "https://raw.githubusercontent.com/cargo-bins/cargo-binstall/main/install-from-binstall-release.ps1").Content
```

**Rust/Cargo:**

本地编译会非常慢

```
cargo install cargo-binstall --locked
```

cargo-binstall 是 Rust CLI 工具的一键部署方案。

比如以下 rust 写的工具：

```sh
# 文件管理器
cargo binstall yazi

# 搜索工具
cargo binstall ripgrep

# Neovim 相关工具
cargo binstall stylua
cargo binstall tree-sitter-cli

# 替代原有的 cargo：
cargo install stylua
cargo install tree-sitter-cli
```

> 注：cargo-binstall 是 Cargo 的子命令，正常命令是 `cargo binstall xxx`, 如果不安装 Cargo，那么就直接执行 `cargo-binstall xxx` 命令即可。



## 找不到 cargo 解决方法

```
echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> $HOME/.zshrc
```
