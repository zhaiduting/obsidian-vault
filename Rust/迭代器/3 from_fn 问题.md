---
tags:
  - clippings
---

**You**

迭代器的本质是结构体类型？那 std::iter::from_fn 得到的迭代器也是结构体？

---

**Gemini**

**是的。** 在 Rust 中，万物皆可为结构体（或者枚举、联合体，但绝大多数迭代器都是结构体）， `std::iter::from_fn` 生成的迭代器也毫无例外，它 **同样是一个结构体** 。

虽然你传入的是一个闭包（Closure），但 Rust 的泛型和宏系统在编译时会为这个闭包量身定制一个结构体。

### 1\. 它是怎么做到的？（查看源码原理）

`std::iter::from_fn` 的底层实现非常巧妙。如果你去翻阅 Rust 标准库的源码，会发现它的定义大概是这样的（简化后）：

```rust
// 标准库定义了一个专门的结构体
pub struct FromFn<F> {
    f: F, // 里面保存了你传进去的闭包
}

// 这个结构体实现了 Iterator trait
impl<T, F> Iterator for FromFn<F>
where
    F: FnMut() -> Option<T>,
{
    type Item = T;

    fn next(&mut self) -> Option<Self::Item> {
        (self.f)() // 每次调用 next，就执行一次你的闭包
    }
}
```

当你调用 `std::iter::from_fn(|| { ... })` 时，编译器在幕后做的事情如下：

1. **自动生成结构体：** 编译器会为你这个特定的闭包创造一个匿名的结构体（在底层它有类似于 `FromFn<你闭包的类型>` 的名字）。
2. **打包状态：** 如果你的闭包捕获了外部的变量（比如一个计数器 `let mut x = 0;`），这些变量就会被 **自动存进这个结构体的字段里** 。
3. **实现 Trait：** 编译器给这个自动生成的结构体实现了 `Iterator` trait，其内部的 `next` 方法就是去调用你写的闭包代码。

### 2\. 代码验证

我们可以写一段代码来直观感受它的状态保存：

```rust
use std::iter;

fn main_demo() {
    let mut count = 0;

    // from_fn 返回的类型就是标准库的 FromFn<impl FnMut() -> Option<i32>>
    let mut counter_iter = iter::from_fn(move || {
        count += 1;
        if count <= 3 {
            Some(count)
        } else {
            None // 结束迭代
        }
    });

    println!("{:?}", counter_iter.next()); // Some(1)
    println!("{:?}", counter_iter.next()); // Some(2)
    println!("{:?}", counter_iter.next()); // Some(3)
    println!("{:?}", counter_iter.next()); // None
}
```

在这个例子中：

- `counter_iter` 是一个实实在在的结构体变量。
- 变量 `count` 因为被闭包捕获，实际上已经被“移动”并打包存进了这个结构体内部。每次调用 `.next()` ，结构体内部的 `count` 状态就会改变。

### 3\. 为什么 Rust 要坚持这样做？（零成本抽象）

你可能会好奇，为什么不搞一个动态分发（比如像 Java 那样用个通用的 `Iterator` 接口对象）？

这就是 Rust 的核心哲学—— **零成本抽象（Zero-cost abstractions）** ：

- 每一个闭包、每一个组合器（比如 `.map()` 、`.filter()` 、 `from_fn` ）在编译后都会变成 **专属的具体结构体** 。
- 因为类型完全确定，编译器可以进行极度激进的优化（比如内联优化、循环展开），最终生成的汇编代码往往比手写的 `while` 循环还要高效。
