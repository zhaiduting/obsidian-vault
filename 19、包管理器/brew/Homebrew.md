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

#### 📝 IDE / 集成开发环境

```sh
brew install --cask visual-studio-code    # 通用编辑器/IDE
brew install --cask phpstorm              # PHP IDE
brew install --cask intellij-idea         # Java/多语言 IDE
brew install --cask goland                # GoLand IDE
brew install go                           # Go 工具链 (可改用 GoLand 安装)
brew install --cask android-studio        # Android 开发 IDE
brew install --cask rustrover             # RustRover IDE 
brew install rust                         # Rust 工具链 (编译器)
```

#### 🛠️ 开发工具

```sh
brew install git                          # 覆盖 macOS 自带的 git
brew install git-gui                      # Git 图形界面
brew install act                          # 本地运行 GitHub Actions
brew install --cask apifox                # API 开发/调试/文档工具（国产）
brew install --cask postman               # API 开发/调试工具
brew install tree                         # 命令行目录树工具
```

#### ⚙️ 开发环境与依赖管理

```sh
brew install node                         # JavaScript 运行环境
brew install yarn                         # Node.js 包管理器
brew install gradle                       # Java 构建与依赖管理工具
brew install --cask google-cloud-sdk      # Google Cloud 开发工具包
```

#### 🗄️ 数据库与存储

```sh
brew install mysql                        # 关系型数据库

brew tap mongodb/brew
brew install mongodb-community            # NoSQL 数据库
```

#### 💻 虚拟化工具

```sh
brew install --cask parallels             # 付费虚拟机（已购）
brew install --cask utm                   # 苹果平台虚拟机（付费版）
brew install --cask crossover             # Mac 直接运行 Windows 程序（已购 2024）
brew install --cask virtualbox            # 免费跨平台虚拟机
brew install --cask docker                # 容器与虚拟化环境管理

```

#### 🧩 系统工具与输入增强

```sh
brew install --cask keka                  # 压缩与解压工具，改用 App Store 付费版
brew install --cask logi-options-plus     # 罗技鼠标管理程序
brew install --cask karabiner-elements    # 高级键盘映射与输入定制工具
```

#### 🌐 浏览器

```sh
brew install --cask google-chrome         # 谷歌浏览器
brew install --cask firefox               # 火狐浏览器
```

#### 📡 通讯与传输

```sh
brew install --cask aliwangwang           # 阿里旺旺
brew install --cask telegram              # 电报（或网页版）
brew install --cask qq                    # QQ
brew install --cask wechat                # 微信
brew install --cask localsend             # 局域网文件传输工具
```

#### 📚 知识管理与效率工具

```sh
brew install --cask obsidian              # 笔记/知识管理（Markdown）
brew install --cask eudic                 # 欧路词典
brew install --cask snagit                # 截图+录屏工具（改用 AppStore 收费版）
brew install --cask picgo                 # 图床工具（文档/博客辅助）
```

#### 🤖 AI 工具

```sh
brew install --cask chatgpt-atlas         # ChatGPT 桌面客户端
```

#### 🎮 游戏与娱乐

```sh
brew install --cask steam                 # 游戏平台
brew install --cask battle-net            # 暴雪游戏平台
```
