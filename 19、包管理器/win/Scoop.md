---
source: /app/86c524873a3008e9
created: 2026-08-25
tags:
  - clippings
---

**Scoop** 是 Windows 上一款专为 **开发者和命令行偏好者** 设计的轻量级命令行包管理器。它的核心哲学是“绿色便携、不污染系统”。

### Scoop 的核心特点

1. **用户态安装，无需管理员权限（No UAC）**
   - 默认将软件安装在用户家目录 `~/scoop/` 下，安装过程极少触发 UAC 提权弹窗。

2. **绿色便携与环境隔离**
   - 优先提取解压软件的便携版（Portable），而不是运行带有各种静默参数的第三方 installer。
   - **不污染系统环境变量** ：Scoop 会在 `~/scoop/shims/` 下生成轻量级垫片（Shim）并统一加入路径，避免将数十个软件路径塞爆 `%PATH%` 。

3. **数据与配置持久化（Persist）**
   - 升级软件时，Scoop 会自动保留用户配置和数据文件夹（通过软链接链接到 `~/scoop/persist/` ），避免软件更新覆盖个人设置。
4. **透明易维护的“Bucket”（桶）机制**
   - 每一个包配方（Manifest）只是一个极其简短的 JSON 文件，维护者和用户都能轻松阅读、修改或自定义私有软件桶（如团队内部工具链）。

### 与 Chocolatey、WinGet 的横向对比

| 维度             | **Scoop**                         | **WinGet** (Windows Package Manager)      | **Chocolatey (Choco)**                  |
| ---------------- | --------------------------------- | ----------------------------------------- | --------------------------------------- |
| **背景/定位**    | 社区驱动的开发利器                | **微软官方** 推出的系统级包管理器         | 老牌 Windows 包管理器，偏 **企业级**    |
| **安装机制**     | 解压绿色版 / 便携版               | 触发软件官方 `.exe` / `.msi` 静默安装     | 执行 PowerShell 自动化脚本运行安装程序  |
| **权限要求**     | **默认 User 权限** （无需管理员） | 取决于软件本身（常触发 UAC）              | **默认需要 Administrator 权限**         |
| **软件目录**     | 统一存放在 `~/scoop/apps/`        | 散落入系统 `Program Files` 或 `%AppData%` | 散落在 `ProgramData` 或系统标准安装路径 |
| **注册表与卸载** | 基本不写入注册表，删除目录即卸载  | 正常写入系统注册表/控制面板“添加与卸载”   | 会在控制面板留下卸载项                  |
| **优势场景**     | 开发者工具、CLI 工具、开源软件    | 常用桌面 GUI 软件、商业软件               | 企业批量部署、大型复杂系统组件          |

### 总结与选型建议

- **选 Scoop** ：如果你是 **开发者 / 极客** ，追求系统干净、经常需要安装 Node.js, Go, Rust, Git, Neovim, ffmpeg 等工具，且讨厌配置环境变量和 UAC 弹窗。
  Reddit
- **选 WinGet** ：如果你需要安装 **Steam, Chrome, Office, Visual Studio** 等大型 GUI 软件，或者需要原生集成 Windows 系统更新。
  Reddit
- **搭配使用** ：当前最佳实践通常是 **Scoop + WinGet 混合使用** ——CLI 工具和开发环境交给 Scoop，大型软件和常规桌面应用交给 WinGet。
  Reddit

---

### 安装命令

在 PowerShell 中（无需管理员权限的终端）执行以下两条命令

```sh
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression
```

> [!TIP]
> **安装 Scoop 不需要（也不建议使用）管理员权限终端。** 因为命令时加上了 `-Scope CurrentUser` ，它只会把软件和配置全部写入当前用户目录 `C:\Users\xxx\scoop\` ，完全绕过了 UAC 提权。

### 相关链接

- **官方网站** ：
  [https://scoop.sh/](https://scoop.sh/)
- **GitHub 开源仓库** ：
  [https://github.com/ScoopInstaller/Scoop](https://github.com/ScoopInstaller/Scoop)
- **官方应用/Bucket 搜索** ：
  [https://scoop.sh/#/apps](https://scoop.sh/)
