虽然 `main.rs` 和 `lib.rs` 都在同一个项目的 `src/` 目录下，但在 Rust 的构建系统里，它们确实被视为**两个独立的编译单元（Crate）**。

要把它们连起来，你需要遵循以下三个步骤：

### 1. 确认 Crate 的名称

在 Rust 中，二进制 crate（`main.rs`）访问库 crate（`lib.rs`）时，必须使用**项目名称**作为路径的起点。

查看你的 `Cargo.toml` 文件：

```toml
[package]
name = "mod_atlas" # 这个名字就是 lib 暴露出的 Crate 名称
```

### 2. 在 `main.rs` 中引用

你不能在 `main.rs` 里使用 `crate::` 来找 `lib.rs` 里的东西。因为在 `main.rs` 里，`crate::` 指向的是 `main.rs` 自己。

你必须像使用第三方库（比如 `serde` 或 `rand`）一样，通过项目名来引用：

```rust
// src/main.rs

fn main() {
    // 方式 A：使用全路径（项目名::函数名）
    mod_atlas::lib_pub();

    // 方式 B：先 use 引入再调用
    use mod_atlas::a::b::c;
    c::c_pub();
}
```

### 3. 编译器是怎么做到的？

当你执行 `cargo run` 时，Cargo 会进行“两步走”操作：

1. **首先编译 `lib.rs`**：生成一个库文件（在 Linux 上是 `.rlib`）。
2. **然后编译 `main.rs`**：在编译 `main` 时，Cargo 会自动向编译器（`rustc`）传递一个参数，告诉它：“嘿，这里有一个叫 `mod_atlas` 的库，我已经帮你编好了，直接链接过去。”

---

### ⚠️ 两个致命的误区

#### 误区一：在 `main.rs` 里写 `mod lib;`

千万不要在 `main.rs` 里写 `mod lib;`。

- 如果你写了 `mod lib;`，Rust 会尝试去寻找 `src/lib.rs` 并将其作为 `main` 的**子模块**再次编译。
- 这会导致你的代码被编译了两次，产生各种奇怪的类型不匹配错误。

#### 误区二：在 `main.rs` 里使用 `crate::` 访问 lib

- `main.rs` 里的 `crate::` $\rightarrow$ 找 `main.rs` 内部定义的函数。
- `main.rs` 里的 `mod_atlas::` $\rightarrow$ 跨过边界找 `lib.rs` 的函数。

---

### 🔍 深度总结：路径起点的区别

| **起点**          | **在 lib.rs 中代表什么** | **在 main.rs 中代表什么**         |
| ----------------- | ------------------------ | --------------------------------- |
| **`crate::`**     | 指向 `lib.rs` 的根       | 指向 `main.rs` 的根               |
| **`mod_atlas::`** | 指向自己（很少这么写）   | **指向 `lib.rs` 的根**            |
| **`super::`**     | 向上跳一级               | 向上跳一级（无法跳出 crate 边界） |

### 下一步验证

既然你已经有了 `mod_atlas` 项目，请打开 `src/main.rs`，尝试输入以下代码并运行 `cargo run`：

```rust
fn main() {
    // 替换成你在 Cargo.toml 里起的名字
    mod_atlas::lib_pub();
}
```

如果报错说找不到 `mod_atlas`，请检查 `Cargo.toml` 里的 `name` 字段是否完全一致。
