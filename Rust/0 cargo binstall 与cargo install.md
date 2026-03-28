以 `dioxus-cli` 为例，以下任意一条命令都可以安装它

- `cargo install dioxus-cli`
- `cargo binstall dioxus-cli`

> [!TIP]
> 首选命令是第二条，但是可能会遇到网络故障。
> 如果 `binstall` 报错 no such command，可以通过 `cargo install cargo-binstall` 解决这个问题。

为了帮你彻底理清两者的区别，我们把它们放在 2023 款 MacBook Pro (M2) 的开发场景下对比：

---

### 1. cargo install（传统手工模式）

当你运行 `cargo install dioxus-cli` 时，发生了以下过程：

1. **下载源码**：从 GitHub/Crates.io 下载几百个 `.rs` 源文件。
2. **下载依赖**：下载 `dioxus-cli` 依赖的几百个第三方库（如 `tokio`, `serde`, `openssl` 等）。
3. **现场编译**：你的 M2 芯片开始满载运行，调用 `rustc` 把这几百万行代码编译成二进制文件。
4. **耗时**：即便 M2 很快，通常也要 **3-10 分钟**。
5. **风险**：如果你的 macOS 缺少某个底层的 C 库（比如 `pkg-config`），编译就会直接报错中断，你还得去修环境。

---

### 2. cargo binstall（顺丰快递模式）

当你运行 `cargo binstall dioxus-cli` 时：

1. **识别架构**：它发现你是 `apple-darwin-aarch64`（M2 芯片原生架构）。
2. **拉取成品**：它直接去 Dioxus 官方的 GitHub Release 页面，寻找已经由官方服务器编译好的 **`.tar.gz` 压缩包**。
3. **解压即用**：下载下来，解压，把可执行文件 `dx` 扔进你的 `~/.cargo/bin`。
4. **耗时**：取决于网速，通常只需 **5-20 秒**。
5. **优势**：**不需要本地有编译环境**。只要能联网下载，就能百分之百安装成功。

---

### 3. 为什么不直接用 cargo install？

既然你有 `binstall`，通常不建议直接 `install` 的原因有三：

| **维度**     | **cargo install**            | **cargo binstall**               |
| ------------ | ---------------------------- | -------------------------------- |
| **等待时间** | 像在餐厅等大厨现炒（慢）     | 像在超市买现成的便当（快）       |
| **资源消耗** | 电脑风扇狂转，电池掉电快     | 几乎不占 CPU，跟下载个图片没区别 |
| **报错几率** | 容易因为缺少底层系统库而失败 | 只要二进制包存在，基本不会失败   |

---

### 4. 什么时候必须用 cargo install？

虽然 `binstall` 很爽，但有两种情况你还得用传统的 `install`：

1. **官方没提供成品**：有些小众插件的作者没在 GitHub 放编译好的包，`binstall` 会找不到。
2. **你需要特殊优化**：你想针对 M2 芯片开启某些极其特殊的本地编译优化参数（这在 99% 的 CLI 工具开发中都没必要）。

---

### 💡 总结

你既然已经安装好了 `binstall`，以后安装任何 Rust 工具（比如 `cargo-expand`, `cargo-watch`, `dioxus-cli`），**第一反应应该是先用 `binstall`**。如果它报错说找不到包，再退回到 `cargo install`。
