---
source: /app/56192e1687031e22
---

```rust
fn returns_summarizable() -> impl Summary {
    SocialPost {
        username: String::from("horse_ebooks"),
        content: String::from(
            "of course, as you probably already know, people",
        ),
        reply: false,
        repost: false,
    }
}
```

### 对比表

| **返回方式**        | **写法**                | **适用场景**   | **备注**        |
| --------------- | --------------------- | ---------- | ------------- |
| **静态分发 (最常用)**  | `-> impl Summary`     | 总是返回同一种类型  | 性能最好，无堆分配     |
| **动态分发 (堆)**    | `-> Box<dyn Summary>` | 可能返回多种不同类型 | 有运行时开销，最灵活    |
| **引用分发 (栈/静态)** | `-> &dyn Summary`     | 返回已存在的对象引用 | 必须标注生命周期 `'a` |

### 为什么 `-> dyn Summary` 是错的？

在 Rust 中，函数返回值必须存放在堆栈（Stack）上。正如我们之前讨论过的，`dyn Summary` 是一个**不可知大小类型（Unsized Type）**。

- 编译器不知道要为 `dyn Summary` 预留多少字节。
- 编译器会报错：`sizable at compile time` (没有实现 `Sized` trait)。

> [!TIP]
> 未完待续……
