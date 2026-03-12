> [!TIP]
> 为啥程序里可以直接使用 `Ok(T)` 而无需使用 `Result::Ok(T)` 这样的写法？

在 Rust 中，这种“不请自来”的便利性源于一个特殊的机制：**预导入（Prelude）**。

---

## 1. 什么是 Prelude？

通常情况下，如果你定义了一个 `enum`，你确实必须通过 `MyEnum::Variant` 来调用它。但 `Option` 和 `Result` 太过常用（几乎每个 Rust 文件都会用到），如果每次都写 `Result::Ok` 或 `Option::Some`，代码会变得极其臃肿。

为了解决这个问题，Rust 编译器在每个模块的开头都隐式地注入了一行代码：

```rust
use std::prelude::v1::*;
```

在这个“标准库预导入包”中，Rust 已经帮你把 `Ok`、`Err`、`Some`、`None` 统统**直接引入了当前作用域**。

---

## 2. 这种写法合法吗？

完全合法。在 Rust 中，你可以通过 `use` 语句将枚举的变体直接“提到”顶层：

```rust
enum MyColor {
    Red,
    Blue,
}

// 如果不写这行，你必须用 MyColor::Red
use MyColor::*;

fn main() {
    let c = Red; // 现在可以直接用了！
}
```

Rust 对 `Result` 和 `Option` 做的正是这件事，只不过它是**全局自动完成**的。

---

## 3. 为什么要保留 Result::Ok 这种写法？

虽然我们可以直接写 `Ok`，但 `Result::Ok` 在某些特定场景下依然有用：

- **消除歧义**：如果你自己定义了一个也叫 `Ok` 的东西（虽然极其不建议这样做），使用全称可以避免混淆。
- **函数式编程**：当你把 `Ok` 作为一个函数闭包传递时，有时写全称更清晰。

  ```rust
  let results: Vec<Result<i32, ()>> = vec![1, 2, 3].into_iter().map(Result::Ok).collect();
  ```

---

## 💡 拓展：除了 Ok/Err，还有什么被预导入了？

Prelude 不仅仅引入了这两个枚举，它还引入了许多“空气一样自然”的东西：

- **常用 Trait**：比如 `Clone`, `Copy`, `Drop`, `Iter`（所以你可以直接调 `.clone()`）。
- **常用宏**：比如 `println!`, `vec!`, `panic!`。
- **String 和 Vec**：这也是为什么你不需要写 `std::string::String` 就能直接用 `String` 的原因。

---

## 相关笔记

- [Ok 必须使用 Result:: 前缀的情形](Ok%20必须使用%20Result%20前缀的情形.md)
