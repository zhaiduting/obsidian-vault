---
created: 2026-02-23
source: https://gemini.google.com/app/ece456207a35cf62
---

在 Rust 中，`write!` 是一个非常实用且高效的宏，它主要用于将**格式化后的字符串**写入到一个特定的“目的地”（Buffer）。

你可能更熟悉 `println!`，但 `println!` 只能把内容发往标准输出。而 `write!` 则更加通用：只要一个类型实现了 `std::fmt::Write` 或 `std::io::Write` trait，你就可以用 `write!` 往里塞数据。

---

## 1. 基本语法

`write!` 的第一个参数是**目标的引用**，后续参数则与 `format!` 相同。

```rust
write!(target, "格式化字符串", 参数1, 参数2);
```

---

## 2. 常见使用场景

### A. 写入到 `String` (内存缓冲)

当你需要频繁拼接字符串时，用 `write!` 往 `String` 里写通常比用 `+` 号或 `format!` 更高效，因为它避免了多次分配内存。

```rust
use std::fmt::Write; // 注意：写入 String 需要导入 std::fmt::Write

fn main() {
    let mut s = String::new();
    let name = "Rust";

    // 将格式化内容追加到 s 中
    write!(&mut s, "Hello, {}!", name).unwrap();
    write!(&mut s, " Welcome to the club.").unwrap();

    println!("{}", s); // 输出: Hello, Rust! Welcome to the club.
}
```

### B. 写入到文件或标准输出 (I/O)

如果要写入文件或网络流，需要使用 `std::io::Write`。

```rust
use std::io::Write; // 注意：这里导入的是 io 模块下的 Write
use std::fs::File;

fn main() -> std::io::Result<()> {
    let mut file = File::create("log.txt")?;

    // 直接将格式化内容写入文件，无需中间变量
    write!(file, "Status: {}, Code: {}", "OK", 200)?;

    Ok(())
}
```

### C. 自定义类型实现 `Display`

这是 `write!` 最核心的舞台。当你为自定义结构体实现打印格式时，必须使用它。

```rust
use std::fmt;

struct Point { x: i32, y: i32 }

impl fmt::Display for Point {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // f 是一个实现了 fmt::Write 的目的地
        write!(f, "坐标是 ({}, {})", self.x, self.y)
    }
}
```

---

## 3. `write!` vs `writeln!`

就像 `print!` 和 `println!` 的关系一样：

- **`write!`**: 只写入你指定的内容。
- **`writeln!`**: 在写入内容后，自动额外追加一个**换行符** (`\n`)。

---

## 4. 核心区别与注意事项

| **特性**     | **write!**                                   | **println!**            | **format!**                |
| ------------ | -------------------------------------------- | ----------------------- | -------------------------- |
| **输出目标** | 任意实现 Write 的对象 (String, File, Vec 等) | 仅限标准输出 (Stdout)   | 返回一个新的 String        |
| **返回值**   | 返回 `Result<(), Error>` (必须处理)          | 无返回值 (会直接 panic) | 返回 String                |
| **性能**     | 高 (可重用已有的 Buffer)                     | 中                      | 较低 (每次都创建新 String) |

> [!IMPORTANT]
>
> **必须处理返回值**：与 `println!` 不同，`write!` 可能会失败（例如磁盘满了或连接断开了）。它返回一个 `Result`。在快速原型中常用 `.unwrap()`，但在正式代码中建议使用 `?` 向上抛出异常。
