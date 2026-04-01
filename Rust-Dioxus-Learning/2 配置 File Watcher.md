它是 JetBrains 官方出品的文件监控器插件，作用：在 IDE 中更改或保存文件时，自动运行编译器、格式化程序或 linter 等命令行工具。本文介绍它的应用场景场景之一，格式化 RSX 代码。

目前为止最新版的 RustRover 2025.3.5 里面，即使设置了 IDE 在保存文件时自动格式化代码，但是对于 RSX 仍然无效。这个问题的解决方法是：安装 `File Watcher` 并添加一个自定义的监控器。

### 准备工作

安装插件：[官方链接](https://plugins.jetbrains.com/plugin/7177-file-watchers)
安装 [dioxus-cli](1%20安装%20dioxus-cli.md)

### 配置插件

![配置Rust文件监听器](https://lib.zhaiduting.work.gd/uPic/%E9%85%8D%E7%BD%AERust%E6%96%87%E4%BB%B6%E7%9B%91%E5%90%AC%E5%99%A8.png)
在 `设置 » 工具 » File Watcher` 界面点击加号按钮添加一个 `<custom>`

- 要监控的文件类型：Rust 文件
- 文件保存时运行的程序：dx
- 实参：`fmt --file $FilePath$`
- 输出路径：`$FilePath$`
- 取消高级选项中的所有对勾
- 给自定义配置取个名字，例如：Dioxus Fmt

### 启用

![启用Rust文件监听器](https://lib.zhaiduting.work.gd/uPic/%E5%90%AF%E7%94%A8Rust%E6%96%87%E4%BB%B6%E7%9B%91%E5%90%AC%E5%99%A8.png)
在 `设置 » 工具 » 保存时的操作` 界面进行如下设置

- 勾选：重新设置代码格式
- 勾选：重新排列代码
- 勾选：`File Watcher: Dioxus Fmt`

### 测试

将以下 `rsx!{...}` 花括号中的代码打乱，然后保存。如果代码能再次还原，则表明配置生效。

```rust
use dioxus::prelude::*;

#[component]
fn App() -> Element {
    rsx! {
        div { "Hello, world!" }
    }
}
```
