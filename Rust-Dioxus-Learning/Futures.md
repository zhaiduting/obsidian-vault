以下异步代码块创建了一个期约，但并不会输出 Ran，因为期约是惰性的。

```rust
let future = async {
    println!("Ran");
};
```

### 启动期约的两种方式

- await
- spawn

示例如下

```rust
let future = async {
    println!("Ran");
};
let other_future = async {
    future.await;
    println!("Ran Other");
};
spawn(other_future);
```

> [!TIP]
> 这里的 `spawn` **既不是 Rust 标准库的函数，也不是 `std::thread::spawn`，而是 Dioxus 提供的异步任务启动函数**。可以通过 `use dioxus::prelude::*` 引入它

### 自动 spawn

Dioxus 事件处理器允许以下两种返回值类型

- `()`：普通同步事件处理器。
- `Future`：异步事件处理器，Dioxus 自动 spawn。

以下明确使用 `spawn()` 的闭包返回值类型为 `()`，如果省略了最后一个分号将导致返回值类型不匹配。

```rust
onclick: move |_| {
    spawn(async move {
        some_async_function().await;
    }); // 本行分号不能省略！
},
```

实际上，利用 Dioxus 自动 spawn 的机制可以简化以上代码，改成如下形式后仍然正确。此时闭包返回值类型是 Future

```rust
onclick: move |_| {
    // spawn(
    async move {
        some_async_function().await;
    }
    // ); // 本行分号不能省略！
},
```

再删除最外层的花括号，进一步简化后最终代码如下

```rust
onclick: move |_| async move {
    some_async_function().await;
},
```
