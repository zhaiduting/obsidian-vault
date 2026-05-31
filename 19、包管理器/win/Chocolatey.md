 
[Chocolatey](https://community.chocolatey.org/)是 Windows 上最受欢迎的包管理器，它允许你通过命令行安装、更新和管理软件包。

### 安装方法

以管理员身份运行终端 PowerShell.exe 后执行如下命令
`Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))`
稍等几秒，如果没有报错则安装成功。

重新打开终端，敲入命令`choco -v`可查看 Chocolatey 版本号

### 使用方法

Chocolatey 的用法很简单，记住以下 3 条就够了：

- 安装命令`choco install xxx`
- 卸载命令`choco uninstall xxx`
- 升级命令`choco upgrade xxx`

例如以下命令
`choco install wechat` 安装微信
`choco install googlechrome` 安装谷歌浏览器

### 常用软件的安装命令集

`choco install logitech-options-plus` 罗技键鼠工具

`choco install fscapture` 截图软件
`choco install 7zip` 文件压缩解压工具
`choco install notepadplusplus` 文本编辑器 Notepad++
`choco install folder-marker` 自定义图标工具

`choco install tencentqq` 腾讯 QQ
`choco install wechat` 微信
`choco install obsidian`
`choco install intellijidea-ultimate` 旗舰版（最好的版本）
`choco install googlechrome --force --ignore-checksums -y`

> [!TIP]
> 谷歌浏览器可能会因为哈希校验不通过导致安装失败，加上 `--ignore-checksums` 就可以取消校验了。安装命令中各个选项的含义如下
> - `--force`: 别废话，哪怕装过也给我重新下载一遍。
> - `--ignore-checksums`: 忽略哈希校验。
> - `-y`: 全程自动点“是”，不用你手动输入 `a` 或者 `y` 了。
