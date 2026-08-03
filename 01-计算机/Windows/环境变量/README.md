# windows 环境变量配置指南

## 什么是环境变量？

环境变量（Environment Variables）是操作系统用来存储配置信息的键值对（Key-Value Pairs）。它们定义了系统的运行环境（如：临时文件存哪、可执行文件去哪找、当前用户名是谁）。


Windows 上环境变量有三个作用域(scopes)。

1. 系统作用域
    - 存储位置：注册表 HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment
    - 生效范围：对所有用户及所有进程生效。
2. 用户作用域
    - 存储位置：注册表 HKEY_CURRENT_USER\Environment
    - 生效范围：仅对当前登录用户生效。
3. 进程作用域
    - 存储位置：内存（当前运行的 CMD 或 PowerShell 进程）。
    - 生效范围：仅对当前运行的程序（如当前的 CMD 窗口或某运行中的 .exe）生效，窗口或程序关闭即消失。


**【覆盖逻辑】**：
- **Path 变量**：系统级 Path 和 用户级 Path 会合并（系统级路径排在前面，优先被搜索）。
- **其他变量**：如果同名，用户变量会覆盖系统变量。



通过 POWESHELL 修改环境变量只能修改**当前会话**的环境变量，除非使用 [System.Environment](https://zhida.zhihu.com/search?content_id=242256120&content_type=Article&match_order=1&q=System.Environment&zhida_source=entity) 类方法

## 核心变量分类速查表

### 系统级 (System / Machine Scopes)

这类变量对计算机上的所有用户都生效，反映的是操作系统或硬件的全局信息。

#### 核心系统路径

| 变量名                    | 用途描述                           | 值                                  | shell           |
|---------------------------|------------------------------------|-------------------------------------|-----------------|
| %SystemRoot%              | Windows 系统根目录                 | C:\Windows                          | CMD, PowerShell |
| %windir%                  | Windows 系统根目录                 | C:\Windows                          | CMD, PowerShell |
| %COMSPEC%                 | 系统命令解释器路径（cmd.exe）      | C:\Windows\System32\cmd.exe         | CMD, PowerShell |
| %ProgramFiles%            | 默认程序安装目录（64 位）          | C:\Program Files                    | CMD, PowerShell |
| %ProgramFiles(x86)%       | 32 位程序安装目录（64 位系统专用） | C:\Program Files (x86)              | CMD, PowerShell |
| %ProgramData%             | 所有用户的程序数据目录             | C:\ProgramData                      | CMD, PowerShell |
| %PUBLIC%                  | 公共用户共享文件夹                 | C:\Users\Public                     | CMD, PowerShell |
| %SystemDrive%             | 操作系统所在的磁盘盘符             | C:                                  | CMD, PowerShell |
| %CommonProgramFiles%      | 64位通用程序共享组件目录           | C:\Program Files\Common Files       | CMD, PowerShell |
| %CommonProgramFiles(x86)% | 32位通用程序共享组件目录           | C:\Program Files (x86)\Common Files | CMD, PowerShell |
| %ComSpec%                 | 默认的命令行解释器(cmd)”存放在哪   | C:\WINDOWS\system32\cmd.exe         | CMD, PowerShell |

#### 硬件信息

| 变量名                   | 用途描述                          | 值                                                         | shell           |
|--------------------------|-----------------------------------|------------------------------------------------------------|-----------------|
| %OS%                     | 操作系统名称（固定为 Windows_NT） | Windows_NT                                                 | CMD, PowerShell |
| %PROCESSOR_ARCHITECTURE% | 处理器架构（如 AMD64、ARM64）     | AMD64                                                      | CMD, PowerShell |
| %NUMBER_OF_PROCESSORS%   | 处理器核心数量                    | 4（根据实际硬件）                                          | CMD, PowerShell |
| %COMPUTERNAME%           | 当前计算机的网络名称              | xxx-xxx                                                    | CMD, PowerShell |
| %PROCESSOR_IDENTIFIER%   | 处理器的详细品牌与型号描述        | xxxx                                                       | CMD, PowerShell |
| %PROCESSOR_LEVEL%        | 处理器家族/分级                   | 0                                                          | CMD, PowerShell |
| %PATHEXT%                | 系统认为的可执行文件后缀名列表    | .COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC;.CPL | CMD, PowerShell |


### 用户级(User Scopes)

这类变量随当前登录的用户改变而改变，存储在用户的个人配置文件中。

#### 个人信息与目录

| 变量名                               | 用途描述                                                    | 值                                               | shell           |
|--------------------------------------|-------------------------------------------------------------|--------------------------------------------------|-----------------|
| %USERNAME%                           | 用户名                                                      | <用户名>                                         | CMD, PowerShell |
| %USERDOMAIN%                         | 用户所属的域或计算机名                                      | DESKTOP-VUOBA4E                                  | CMD, PowerShell |
| %USERPROFILE%                        | 当前用户配置文件目录                                        | C:\Users\<用户名>                                | CMD, PowerShell |
| %HOMEPATH%                           | 用户主目录相对路径                                          | C:\Users\<用户名>                                | CMD, PowerShell |
| %HOMEDRIVE%                          | 用户主目录所在驱动器（如 C:）                               | C:                                               | CMD, PowerShell |
| %OneDrive%                           | OneDrive 同步文件夹路径                                     | C:\Users\<用户名>\OneDrive                       | CMD, PowerShell |
| %LOGONSERVER%                        | 验证当前用户登录的服务器名(域环境下非常重要)                | \\DESKTOP-T2C477V                                | CMD, PowerShell |
| %USERDNSDOMAIN%                      | 用户所属的完整 DNS 域名,企业/域环境常用                     | xxx                                              | CMD, PowerShell |
| %LOCALAPPDATA%\Programs              | 现代非管理员软件的安装根目录, VS Code, Python 等默认在此    | C:\Users\ll\AppData\Local\Programs\              | CMD, PowerShell |
| %LOCALAPPDATA%\Microsoft\WindowsApps | Windows 商店应用(UWP)执行路径, 存放 wt.exe, python.exe 别名 | C:\Users\ll\AppData\Local\Microsoft\WindowsApps\ | CMD, PowerShell |


#### 应用与临时数据

| 变量名         | 用途描述             | 值                                   | shell           |
|----------------|----------------------|--------------------------------------|-----------------|
| %APPDATA%      | 用户漫游程序数据目录 | C:\Users\<用户名>\AppData\Roaming    | CMD, PowerShell |
| %TEMP% / %TMP% | 临时文件夹路径       | C:\Users\<用户名>\AppData\Local\Temp | CMD, PowerShell |
| %LOCALAPPDATA% | 用户本地程序数据目录 | C:\Users\<用户名>\AppData\Local      | CMD, PowerShell |


#### 特殊 Shell 文件夹

注：这些在标准 CMD 环境变量中可能不直接存在，但在某些环境或 Shell 工具中常用

| 变量名      | 用途描述                           | 值                          | shell           |
|-------------|------------------------------------|-----------------------------|-----------------|
| %Desktop%   | 当前用户的桌面路径                 | C:\Users\<用户名>\Desktop   | CMD, PowerShell |
| %Documents% | 当前用户的文档文件夹               | C:\Users\<用户名>\Documents | CMD, PowerShell |
| %Downloads% | 用户下载文件夹（依赖系统或自定义） | C:\Users\<用户名>\Downloads | CMD, PowerShell |


### 动态/运行时变量(Volatile / Dynamic)

这些变量的值是由系统或 Shell 实时生成的，无法在“环境变量”设置界面修改，仅在运行时有效。

#### 系统状态

| 变量名          | 用途描述                               | 值                          | shell           |
|-----------------|----------------------------------------|-----------------------------|-----------------|
| %DATE%          | 当前日期（格式依赖系统区域设置）       | 2024-06-20                  | CMD, PowerShell |
| %TIME%          | 当前时间（格式依赖系统区域设置）       | 14:30:45.50                 | CMD, PowerShell |
| %RANDOM%        | 生成一个 0~32767 的随机数              | 12345                       | CMD, PowerShell |
| %ERRORLEVEL%    | 上一个命令的退出代码（0 表示成功）     | 0 或非零错误码              | CMD, PowerShell |
| %CD%            | 当前工作目录的完整路径                 | c:\xx\xxx                   | CMD             |
| %CMDCMDLINE%    | 启动当前 CMD 窗口时的完整原始命令      | C:\WINDOWS\System32\cmd.exe | CMD             |
| %CMDEXTVERSION% | 当前命令扩展（Command Extensions）版本 | 2                           | CMD             |


#### 路径搜索(特殊混合变量)

| 变量名 | 用途描述                   | 值                             | shell           |
|--------|----------------------------|--------------------------------|-----------------|
| %PATH% | 系统可执行文件搜索路径列表 | C:\Windows\system32;C:\Windows | CMD, PowerShell |

### PowerShell 专用变量

这些是以 $ 开头的变量，它们不是标准 Windows 环境变量（Environment Variables），而是 PowerShell 引擎内部维护的变量。

| 变量名          | 用途描述                                                     | 值                                                               | shell      |
|-----------------|--------------------------------------------------------------|------------------------------------------------------------------|------------|
| $HOME           | 当前用户的主目录                                             | C:\Users\<Username>                                              | PowerShell |
| $PROFILE        | PowerShell 配置文件路径                                      | C:\Users\m\Documents\PowerShell\Microsoft.PowerShell_profile.ps1 | PowerShell |
| $PWD            | 当前工作目录                                                 | C:\Projects                                                      | PowerShell |
| $PSHOME         | PowerShell 安装目录                                          | C:\Windows\System32\WindowsPowerShell\v1.0                       | PowerShell |
| $PSVersionTable | 包含 PS 详细版本信息的哈希表, 查看是 5.1 还是 7.x 的核心变量 | xxx                                                              | PowerShell |
| $PID            | 当前 PowerShell 进程的唯一进程 ID, 进程管理                  | 10700                                                            | PowerShell |
| $?              | 判断上一个命令是否执行成功(true/false)                       | true                                                             | PowerShell |
| $LASTEXITCODE   | 上一个 外部程序 返回的退出码, 等同于 CMD 的 %ERRORLEVEL%     | 0                                                                | PowerShell |
| $IsWindows      | 当前是否为 Windows 系统, 跨平台 PS (7+) 专用判断             | ...                                                              | PowerShell |


## 环境变量的操作方法

1. 图形界面(GUI)
    - **快捷键**: `Win + R` - 输入 `sysdm.cpl` 回车确定 - "高级" 选项卡 - 环境变量
    - **版本差异**：Win10/11 提供了表格化编辑器，支持逐行编辑 Path；Win7 及以下版本则是在一行长字符串中编辑。
2. CMD
    - **查看单个变量**: `echo %PATH%`
    - **列出所有变量**: `set`
    - **临时修改 (仅本窗口)**: `set MY_VAR=Hello`
    - **临时追加**: `$env:PATH += ";C:\your\new\path"`
    - **永久修改**：`setx MY_VAR "Value"`
        - 注：setx 默认修改用户变量。若修改系统变量需管理员权限并加 /M。且 setx 有 1024 字符限制。
3. PowerShell
    - **临时查看**：`$env:PATH`
    - 列出所有：`Get-ChildItem Env:` (简写 `dir env:`)
    - **临时修改**：`$env:MY_VAR = "Value"`
    - 永久修改 (通过 .NET 接口)：

```sh
# 永久修改用户变量
[Environment]::SetEnvironmentVariable("MyVar", "Value", "User")


# 永久追加变量
$oldPath = [Environment]::GetEnvironmentVariable("Path", "User")
$newPath = $oldPath + ";C:\your\new\path"
[Environment]::SetEnvironmentVariable("Path", $newPath, "User")
```


## 核心避坑与高级技巧
1. Path 变量的顺序至关重要：当你在命令行输入 python 时，Windows 会按 Path 列表从上到下的顺序查找。如果你的电脑装了两个版本的 Python，排在上面的那个会被执行。
2. 修改后没生效？：通过 GUI 或 setx 修改环境变量后，必须关闭并重新打开命令行窗口，新变量才会生效。对于正在运行的服务（如 VS Code 内置终端），通常需要重启软件。
3. 空格的处理：在路径中含有空格时（如 `C:\Program Files`），在脚本或命令行中使用建议加上双引号：cd `"%ProgramFiles%"`。
4. %OS% 的迷惑性：该变量在所有现代 Windows（Win7/8/10/11）中值都是 Windows_NT，这是为了保持对旧版 NT 内核脚本的兼容性。
5. Path 的长度限制：虽然 NTFS 路径很长，但环境变量总长度是有上限的（通常为 2048 或 4096 字符）。如果 Path 太乱，建议清理无效路径或使用软链接简化路径。
6. 变量作用域：
    - $XXX 是 PowerShell 的变量, 可用 `echo $XXX` 查看变量值.
    - %XXX% 是系统环境变量，需通过 `$env:XXX` 在 PowerShell 中访问。
7. 斜杠方向：
    - PowerShell 同时支持 / 和 \，但建议统一使用反斜杠 \（Windows 风格）。
    - CMD 必须使用 \ 。
8. 路径分隔符: 
    - 一个变量可以有多个值,
    - 不同变量值可使用分号 `;` 进行分隔, 如: `xxx;xxx;xxx`
