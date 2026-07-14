---
source: /c/6a557df9-4b48-83ec-9afc-9b529e5dbdba
created: 2026-07-14
description: ChatGPT
tags:
  - clippings
---

# 以下闭包为什么要使用 move 呢？

```rust
use dioxus::prelude::*;

pub fn App() -> Element {
    let mut count = use_signal(|| 0);

    rsx! {
        h1 { "High-Five counter: {count}" }
        button { onclick: move |_| count += 1, "Up high!" }
        button { onclick: move |_| count -= 1, "Down low!" }
    }
}
```

---

这里的 `move` 主要不是为了「移动 `count` 的所有权」这么简单，而是因为 **Dioxus 的事件闭包需要持有 `count` ，而不是借用 `count`** 。

看这两行：

```rust
button { onclick: move |_| count += 1, "Up high!" }
button { onclick: move |_| count -= 1, "Down low!" }
```

`onclick` 接收的是一个闭包：

```rust
FnMut(Event)
```

这个闭包不会立即执行，而是会被 Dioxus 保存起来，等用户点击按钮时再调用。

---

### 如果去掉 move

比如：

```rust
button { onclick: |_| count += 1, "Up high!" }
```

Rust 会尝试让闭包借用外面的 `count` ：

大概等价于：

```rust
button {
    onclick: |event| {
        &mut count;
        count += 1;
    }
}
```

也就是闭包里面捕获的是：

```rust
&mut count
```

问题来了：

`App()` 函数执行完以后：

```rust
pub fn App() -> Element {
    let mut count = use_signal(|| 0);

    rsx! {
        button { onclick: |_| count += 1 }
    }
}
```

函数返回：

```rust
Element
```

但是 `onclick` 里的闭包还要继续存在。

生命周期类似：

```
App()
 |
 | 创建 count
 |
 | 创建 onclick 闭包
 |
 | 返回 Element
 |
 ↓
函数结束
```

如果闭包里面保存的是：

```
&mut count
```

那么函数结束后：

```
count 已经不存在
       ↑
       |
闭包还在引用它
```

这就是悬垂引用，所以 Rust 不允许。

---

### 加上 move 后

```rust
button { onclick: move |_| count += 1 }
```

意思是：

> 不要借用外面的变量，把它捕获进闭包自己的环境中。

类似：

```rust
let count = count;

let handler = move |_| {
    count += 1;
};
```

现在结构变成：

```
App()
 |
 | 创建 Signal
 |
 | move 到闭包
 |
 | Element 保存闭包
 |
 ↓
App结束

闭包内部仍然拥有 count
```

生命周期就正确了。

---

### 那为什么 count 可以被两个按钮同时 move？

你可能会疑惑：

```rust
button { onclick: move |_| count += 1 }

button { onclick: move |_| count -= 1 }
```

不是两次移动吗？

正常 Rust 里：

```rust
let x = String::new();

move || println!("{}", x);
move || println!("{}", x); // ❌
```

第二次会报：

```
use of moved value
```

但是 Dioxus 的 `Signal<T>` 和普通变量不同。

`use_signal` 返回的是：

```rust
Signal<i32>
```

它内部类似：

```
Signal
 ├── 指向状态存储
 └── 可以复制句柄
```

💡 `Signal<T>` 实现了 `Copy` （对于满足条件的情况），所以移动进去实际上复制了一份句柄：

```
count
  |
  +----> button 1 closure
  |
  +----> button 2 closure
```

两个闭包操作的是同一个 signal。

---

### 为什么 Dioxus 示例里几乎总写 move

因为事件、异步任务、effect 等场景，经常要求闭包满足：

```rust
'static
```

也就是：

> 这个闭包里面不能保存短生命周期的引用。

例如：

```rust
spawn(async {
    println!("{}", count);
});
```

也需要：

```rust
spawn(async move {
    println!("{}", count);
});
```

原因一样：

异步任务可能在当前函数结束后才执行。

---

所以这段代码里的 `move` 可以理解成：

> 把 React/Dioxus 状态句柄 `count` 交给事件处理函数长期保存，而不是让事件函数借用当前组件栈里的变量。

在 Dioxus 里这是非常常见的模式：

```rust
onclick: move |_| ...
use_effect(move || ...)
spawn(async move { ... })
```

基本看到这些地方，第一反应就是： **这个闭包需要活得比当前函数调用更久，所以必须拥有捕获的数据。**

---

# 基本数据类型是不是都实现了 Copy 特征？

---

在 Rust 里， **大部分基本数据类型（primitive types）都实现了 `Copy` ，但不是所有看起来像“基础”的类型都实现** 。

### 实现 Copy 的基本类型

这些类型默认都是 `Copy` ：

#### 整数

```rust
i8 i16 i32 i64 i128 isize
u8 u16 u32 u64 u128 usize
```

例如：

```rust
let a = 10;
let b = a;

println!("{}", a); // ✅ 可以继续使用
```

因为赋值发生的是复制：

```
a ──> 10

b ──> 10
```

不是移动。

---

#### 浮点数

```rust
f32
f64
```

```rust
let x = 1.5;
let y = x;

println!("{}", x); // ✅
```

---

#### 布尔

```rust
bool
```

```rust
let a = true;
let b = a;

println!("{}", a); // ✅
```

---

#### 字符

```rust
char
```

```rust
let c = '中';
let d = c;

println!("{}", c); // ✅
```

---

#### 不可变引用

引用也实现 `Copy` ：

```rust
let x = 100;

let r1 = &x;
let r2 = r1;

println!("{:?}", r1); // ✅
```

因为复制的是地址：

```
r1 ─┐
    ├──> x
r2 ─┘
```

---

## 不实现 Copy 的常见类型

### String

```rust
let s1 = String::from("hello");
let s2 = s1;

println!("{}", s1); // ❌
```

因为 `String` 拥有堆内存：

```
s1
 |
 v
Heap: "hello"
```

移动后：

```
s2
 |
 v
Heap: "hello"
```

如果复制会导致两个 String 管理同一块内存，释放时会 double free。

---

### Vec

```rust
let v1 = vec![1,2,3];
let v2 = v1;

println!("{:?}", v1); // ❌
```

---

### HashMap、Box 等

也都不是：

```rust
HashMap<K,V>
Box<T>
Rc<T>
Arc<T>
```

---

## 一个容易误解的地方：数组和元组

如果里面的元素都是 `Copy` ，那么它们也可以 `Copy` 。

例如：

```rust
let a = (1, 2, 3);
let b = a;

println!("{:?}", a); // ✅
```

因为：

```rust
(i32, i32, i32)
```

里面全部是 `Copy` 。

但是：

```rust
let a = (String::from("hi"), 10);
let b = a;

println!("{:?}", a); // ❌
```

因为包含 `String` 。

---

## 自定义类型也可以实现 Copy

例如：

```rust
#[derive(Copy, Clone)]
struct Point {
    x: i32,
    y: i32,
}

let p1 = Point { x:1, y:2 };
let p2 = p1;

println!("{}", p1.x); // ✅
```

但是：

```rust
#[derive(Copy, Clone)]
struct User {
    name: String, // ❌
}
```

会失败，因为 `String` 不是 `Copy` 。

---

## 回到 Dioxus 的 Signal

你前面问：

```rust
let mut count = use_signal(|| 0);

button {
    onclick: move |_| count += 1
}
```

这里关键不是 `i32` 是 `Copy` ，而是：

```rust
count: Signal<i32>
```

这个 **Signal 类型本身实现了 Copy** 。

所以：

```rust
move |_| count += 1
```

捕获的是一个 `Signal` 副本：

```
count
 |
 +---- closure 1
 |
 +---- closure 2
```

而不是复制里面的 `i32` ：

```
Signal
  |
  v
 i32 状态
```

多个 `Signal` 副本仍然指向同一个响应式状态。

这也是 Dioxus 能让多个事件闭包同时修改同一个状态的原因。
