可以把 Scoop 常用命令记成几组，日常基本够用了。**核心就四件事：搜、装、更新、清理。** 剩下的是包管理器不可避免的仪式感。😏

## 1. 搜索 / 查看软件

```powershell
scoop search <name>
```

例如：

```powershell
scoop search ffmpeg
```

查看某个包详细信息：

```powershell
scoop info <name>
```

查看官网：

```powershell
scoop home <name>
```

查看 manifest：

```powershell
scoop cat <name>
```

查看依赖：

```powershell
scoop depends <name>
```

Scoop 也能按提供的命令名搜索，例如不知道 `hg` 属于什么包，也可以直接 `scoop search hg`。([GitHub](https://github.com/ScoopInstaller/Scoop/wiki/Quick-Start/76b22a852696fb678538611b69f06e85f81bf318?utm_source=chatgpt.com))

------

## 2. 安装

```powershell
scoop install <name>
```

例如：

```powershell
scoop install ffmpeg
```

指定 bucket：

```powershell
scoop install main/ffmpeg
scoop install extras/vscode
```

一次安装多个：

```powershell
scoop install git ffmpeg uv ripgrep
```

全局安装：

```powershell
scoop install <name> --global
```

简写：

```powershell
scoop install <name> -g
```

全局安装通常需要管理员权限，默认用户安装则位于：

```text
~/scoop/apps
```

全局安装一般位于：

```text
C:\ProgramData\scoop
```

([GitHub](https://github.com/ScoopInstaller/Scoop/wiki/Global-Installs?utm_source=chatgpt.com))

------

## 3. 查看已经安装的软件

```powershell
scoop list
```

过滤：

```powershell
scoop list ffmpeg
```

查看安装目录：

```powershell
scoop prefix ffmpeg
```

例如可能返回：

```text
C:\Users\Jerry\scoop\apps\ffmpeg\current
```

查某个命令到底由 Scoop 哪个 shim 提供：

```powershell
scoop which ffmpeg
```

这个对于排查 **WinGet / Scoop / npm / cargo 同名命令冲突**尤其好用。

------

## 4. 更新 Scoop 和软件

### 更新 Scoop 自己 + bucket manifests

```powershell
scoop update
```

注意它主要更新：

```text
Scoop 本体
bucket manifests
```

**不是更新所有已安装程序。**

### 更新单个程序

```powershell
scoop update ffmpeg
```

### 更新全部程序

```powershell
scoop update *
```

这是最常用的组合：

```powershell
scoop update
scoop update *
```

官方 Quick Start 也是这么定义更新流程的。([GitHub](https://github.com/ScoopInstaller/Scoop/wiki/Quick-Start/b8849c34f2545adc47a10160cc6381b8864319d4?utm_source=chatgpt.com))

------

## 5. 检查有没有更新

```powershell
scoop status
```

它会告诉你：

- Scoop 是否过期
- bucket 是否过期
- 哪些软件有新版本

所以如果只想看看而不更新：

```powershell
scoop status
```

------

## 6. 卸载

```powershell
scoop uninstall <name>
```

例如：

```powershell
scoop uninstall ffmpeg
```

全局软件：

```powershell
scoop uninstall ffmpeg -g
```

### 删除 persist 数据

普通：

```powershell
scoop uninstall <name>
```

通常会**保留 persist 数据**。

如果想连持久化配置一起删除：

```powershell
scoop uninstall <name> --purge
```

这个要谨慎一点。例如某些程序把配置、模型、用户数据放在：

```text
~/scoop/persist/<name>
```

`--purge` 就属于“软件没了，遗产也一并火化”。

------

## 7. 清理旧版本

Scoop 更新以后通常会保留以前的版本：

```text
~/scoop/apps/ffmpeg/
├── 8.1.2
├── 9.0
├── 9.0.1
└── current
```

清理某一个：

```powershell
scoop cleanup ffmpeg
```

清理全部：

```powershell
scoop cleanup *
```

这是很值得定期跑的。

尤其 FFmpeg、LLVM、Python 这种一个版本几百 MB 的东西，不清的话硬盘很快会开始收藏软件化石。

------

## 8. 下载缓存

查看缓存：

```powershell
scoop cache show
```

或者直接：

```powershell
scoop cache
```

删除某个软件缓存：

```powershell
scoop cache rm ffmpeg
```

清空所有缓存：

```powershell
scoop cache rm *
```

所以比较完整的“扫地”操作可以是：

```powershell
scoop cleanup *
scoop cache rm *
```

------

# 9. Bucket 管理

查看 bucket：

```powershell
scoop bucket list
```

添加：

```powershell
scoop bucket add extras
```

删除：

```powershell
scoop bucket rm extras
```

查看已知 bucket：

```powershell
scoop bucket known
```

常见的是：

```text
main
extras
versions
java
games
nerd-fonts
nonportable
```

其中一般：

```text
main
  → CLI、开发工具

extras
  → GUI 软件

versions
  → nightly / beta / 特定版本
```

例如：

```powershell
scoop bucket add extras
scoop install extras/vscode
```

------

# 10. 固定版本，不让它更新

```powershell
scoop hold <name>
```

例如：

```powershell
scoop hold ffmpeg
```

之后：

```powershell
scoop update *
```

会跳过它。

恢复：

```powershell
scoop unhold ffmpeg
```

官方命令集直接提供 `hold` / `unhold` 来控制更新。([GitHub](https://github.com/ScoopInstaller/Scoop/wiki/Commands/eb42eccca24b46fe3eeac52e12f41444e6055116?utm_source=chatgpt.com))

这对开发环境挺重要，比如：

```text
CUDA 工具
编译器
FFmpeg
Python
Node
```

有时“最新版本”是一个功能，有时则是一场事故。

------

# 11. 修复 / 切换当前版本

```powershell
scoop reset <name>
```

例如：

```powershell
scoop reset ffmpeg
```

它会重新设置：

```text
current junction
shims
环境配置
```

如果某次更新后 shim 或 current 链接出了问题，`reset` 很好用。

也可以：

```powershell
scoop reset *
```

不过通常没必要核爆整个环境。

------

# 12. Shim 管理

查看所有 Scoop shims：

```powershell
scoop shim list
```

查看具体 shim：

```powershell
scoop shim info ffmpeg
```

自己创建：

```powershell
scoop shim add mycommand C:\somewhere\program.exe
```

删除：

```powershell
scoop shim rm mycommand
```

现在 Scoop 的 `shim` 子命令支持 `add / rm / list / info / alter`。([GitHub](https://github.com/ScoopInstaller/Scoop/blob/master/libexec/scoop-shim.ps1?utm_source=chatgpt.com))

这个对于你这种 CLI 工具比较多的环境其实挺有用。

------

# 13. 配置

查看全部当前配置：

```powershell
scoop config
```

单独查看：

```powershell
scoop config use_external_7zip
scoop config use_sqlite_cache
```

设置：

```powershell
# 让 scoop 使用全局 7zip
scoop config use_external_7zip true
# 让 scoop 使用 sqlite 加速搜索
scoop config use_sqlite_cache true
```

删除设置，恢复默认：

```powershell
scoop config rm use_external_7zip
scoop config rm use_sqlite_cache
```

------

# 14. 导出 / 恢复整套 Scoop 环境

这个很值得记。

导出：

```powershell
scoop export > scoop.json
```

新版 Scoop 的 export 可以包含已安装应用、bucket，并可选择包含配置。([GitHub](https://github.com/ScoopInstaller/Scoop/issues/6502?utm_source=chatgpt.com))

恢复：

```powershell
scoop import scoop.json
```

所以换机器时可以：

```text
旧电脑
 ↓
scoop export

scoop.json

 ↓
新电脑

scoop import
```

对于开发机重装相当方便。

------

# 15. 检查 Scoop 自己有没有问题

```powershell
scoop checkup
```

会检查：

- PATH
- Git
- 7-Zip
- 权限
- Scoop 目录
- 其他环境问题

如果 Scoop 开始表现得像 WinGet 那样神秘，先跑：

```powershell
scoop checkup
```

至少让问题稍微少一点玄学。

------

# 我建议你实际记住这 16 条

```powershell
# 搜
scoop search <app>

# 信息
scoop info <app>

# 安装
scoop install <app>

# 已安装
scoop list

# 更新 Scoop / manifests
scoop update

# 更新全部软件
scoop update *

# 查看哪些需要更新
scoop status

# 卸载
scoop uninstall <app>

# 完全卸载，包括 persist
scoop uninstall <app> --purge

# 清旧版本
scoop cleanup *

# 清下载缓存
scoop cache rm *

# 管 bucket
scoop bucket list
scoop bucket add extras

# 固定版本
scoop hold <app>
scoop unhold <app>

# 找命令来源
scoop which <command>

# 导出环境
scoop export > scoop.json

# 导入环境
scoop import scoop.json
```

