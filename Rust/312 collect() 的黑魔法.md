这是 Rust 中 `collect()` 最让人惊叹的特性之一。我们称之为 **“全有或全无”（All or Nothing）**。

在处理列表数据时，你经常会遇到这种需求：我有 10 条数据，我要对它们进行解析。

- 如果全部解析成功，给我一个**成功列表**。
- 如果其中**任何一个**失败了，直接给我那个**错误**，剩下的也不用看了。

---

## 魔法展示

在其他语言中，你可能需要写一个循环，不断检查是否有错误。在 Rust 中，你只需要通过类型推导告诉 `collect`：

```rust
use std::num::ParseIntError;

/// 将字符串切片列表解析为整数向量
/// 如果全部解析成功，返回 Ok(Vec<i32>)
/// 只要有一个解析失败，立即返回 Err
fn parse_numbers(strings: Vec<&str>) -> Result<Vec<i32>, ParseIntError> {
    strings
        .into_iter()
        .map(|s| s.parse::<i32>())
        .collect() // 这里的类型推导会自动处理“全有或全无”的逻辑
}
fn main() {
    // 准备两组不同的数据，但都交给同一个函数处理
    let datasets = vec![
        vec!["10", "20", "30"],    // 完美数据
        vec!["100", "Boom", "300"] // 带坑数据
    ];

    for data in datasets {
        println!("--- 正在处理数据: {:?} ---", data);

        // 核心：无论输入是什么，调用的都是同一个逻辑
        match parse_numbers(data) {
            Ok(nums) => println!("成功拿到了 Vec: {:?}", nums),
            Err(e) => eprintln!("由于其中一个出错，整个结果变成了 Err: {}", e),
        }
    }
}
```

执行结果如下

```sh
--- 正在处理数据: ["10", "20", "30"] ---
成功拿到了 Vec: [10, 20, 30]
--- 正在处理数据: ["100", "Boom", "300"] ---
由于其中一个出错，整个结果变成了 Err: invalid digit found in string
```

---

## 它是怎么做到的？

这是因为标准库为 `Result` 实现了一个非常聪明的 `FromIterator` 特征。

当你要求 `collect` 返回一个 `Result<Vec<T>, E>` 时，它的内部逻辑变成了：

1. 创建一个空的 `Vec`。
2. 遍历迭代器中的每个 `Result`。
3. 如果是 `Ok`，把值塞进 `Vec`。
4. 如果是 `Err`，**立即停止**遍历，直接把这个 `Err` 丢出来作为最终结果。

---

## strings 是怎么变成 Result 枚举类型的？

[解释 parse_numbers()](312%20从%20Vec%20到%20Result.md)

---

## 为什么这个功能极其好用？

配合我们之前讲过的 `?` 运算符，你可以写出极度简洁的代码：

```rust
fn process_all_data(data: Vec<&str>) -> Result<Vec<i32>, MyError> {
    // 只要有一个解析失败，整个函数就会通过 ? 提前返回 Err
    let results = data.into_iter()
        .map(|s| s.parse().map_err(MyError::from))
        .collect()?;

    Ok(results)
}
```

---

## 延伸：Option 也可以这样！

同样的逻辑也适用于 `Option`。如果你有一堆 `Option`，你想在全部是 `Some` 时拿到 `Vec`，只要有一个是 `None` 就返回 `None`：

```rust
let values = vec![Some(1), Some(2), None];
let merged: Option<Vec<i32>> = values.into_iter().collect();

// 结果是 None
```

---

### 总结

- **`Vec<Result<T, E>>`**：这是一堆结果，有成功的也有失败的。
- **`Result<Vec<T>, E>`**：这是一个整体的结果，强调的是“整体的完整性”。

这种转换能力是 Rust 类型系统强大表达力的体现。它把复杂的循环控制流（遇到错误就退出）压缩成了一个简单的类型声明。

**这就是 Result 的魅力——它从不让你假装错误不存在，但它提供了足够精妙的工具让你处理错误时像写诗一样优雅。关于 Rust 的错误处理，你还有什么想深挖的“语法糖”吗？**
