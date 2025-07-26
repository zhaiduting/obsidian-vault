**Homebrew** 是 macOS 和 Linux 上的包管理器，用于安装、更新、管理和卸载软件包或工具。它的目标是让用户更容易地在系统中安装 Unix 工具和开源应用。

它的官方口号是：

> “The missing package manager for macOS (or Linux)”

---

### 🛠️ Homebrew 能做什么？

- 安装 CLI 工具（如 `wget`, `git`, `python`, `node`, `ffmpeg` 等）
- 安装 GUI 应用（通过 `brew install --cask`, 如 `google-chrome`, `visual-studio-code`）
- 管理已安装的软件（升级、卸载、查看信息等）
- 支持多个软件版本和自定义安装目录（通过 `brew tap`、`brew link` 等机制）

---

### ✅ Homebrew 的优点

- 安装方便（只需一行命令）
- 安全可靠（使用 Ruby 脚本构建）
- 社区活跃，软件包数量庞大
- 安装路径清晰（一般安装在 `/opt/homebrew` 或 `/usr/local/`）
- 支持自动更新（`brew update`）

---

### 🔧 常用命令

| 命令                    | 作用                           |
| ----------------------- | ------------------------------ |
| `brew install <包名>`   | 安装软件包                     |
| `brew uninstall <包名>` | 卸载软件包                     |
| `brew update`           | 更新 Homebrew 本身             |
| `brew upgrade`          | 升级已安装软件包               |
| `brew list`             | 列出已安装的所有包             |
| `brew info <包名>`      | 查看某个软件包的详细信息       |
| `brew search <关键词>`  | 搜索软件包                     |
| `brew doctor`           | 检查 Homebrew 是否存在配置问题 |

---

### 🖥️ 安装方式

在终端中输入以下命令安装 Homebrew（建议使用官网提供的最新命令）：

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

安装后，根据提示将 Homebrew 路径添加到你的 `~/.zprofile` 或 `~/.bash_profile` 等 shell 配置文件中。

---

### 📁 Homebrew 的目录结构（macOS Apple Silicon）

| 路径                      | 说明                       |
| ------------------------- | -------------------------- |
| `/opt/homebrew/bin/brew`  | brew 命令本体              |
| `/opt/homebrew/Cellar/`   | 安装的软件包目录           |
| `/opt/homebrew/Caskroom/` | GUI 应用（Cask）的安装目录 |
| `/opt/homebrew/etc/`      | 配置文件目录               |
| `/opt/homebrew/lib/`      | 库文件目录                 |

（Intel Mac 使用 `/usr/local` 作为根路径）

---

### 🔌 扩展功能

- **Cask（brew install --cask）**：安装 macOS 的 GUI 应用
- **Tap（brew tap）**：添加第三方仓库
- **Brewfile（`brew bundle`）**：像 `package.json` 一样记录和恢复你的所有软件环境

---

### 🔍 查找软件的安装命令

可以根据以下命令查找

```shell
brew search <模糊匹配的包名>
brew search <精确包名>
```

或者直接在官网文本框中搜索

- 官网：[https://brew.sh](https://brew.sh)
- GitHub：[https://github.com/Homebrew/brew](https://github.com/Homebrew/brew)

---

### 📦 已整理的安装命令

brew install --cask logi-options-plus 罗技鼠标程序
brew install git 覆盖 Mac 自带的 git
brew install git-gui

brew install --cask google-chrome 谷歌浏览器
==brew install --cask obsidian== 支持 markdown 语法的编辑器

brew install node
brew install yarn
brew install gradle

brew install --cask picgo 图床工具

#### IDE

brew install --cask visual-studio-code
brew install --cask phpstorm
brew install --cask intellij-idea 安装 IntelliJ IDEA
brew install --cask goland 安装 GoLand
brew install --cask android-studio

brew install --cask apifox 国产 Api 开发工具
brew install --cask google-cloud-sdk 谷歌云，软件开发工具包

#### 通讯

brew install --cask aliwangwang 阿里旺旺
brew install --cask telegram 电报==(网页版 [https://web.telegram.org/](https://web.telegram.org/) )==
brew install --cask qq
brew install --cask wechat 微信

#### 虚拟机

brew install --cask parallels 付费虚拟机（已购买 v19 和 v20 两个版本）
brew install --cask utm (改用苹果付费程序)
brew install --cask crossover Mac 直接运行 Win 程序（已购买 2024 版）
brew install --cask virtualbox

#### 数据库

brew install mysql

brew tap mongodb/brew
brew install mongodb-community

#### 游戏

brew install --cask steam
brew install --cask battle-net
