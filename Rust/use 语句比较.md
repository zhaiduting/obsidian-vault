---
source: https://chatgpt.com/share/6a6d501f-5e34-83ea-b1b1-d2e3c984e203
created: 2026-08-01
tags:
  - clippings
---

# 指定导入

以下3种写法是否等价？
写法1如下

```rust
use std::thread;
use std::thread::spawn;
```

写法2如下

```rust
use std::thread;
use thread::spawn;
```

写法3如下

```rust
use std::thread::{self, spawn};
```

---

这 3 种写法 **基本等价** ，最终效果都是：把 `std::thread` 模块引入当前作用域，并且可以直接使用 `spawn()` 。

不过它们在语法含义上有一点区别。

---

## 写法 1

```rust
use std::thread;
use std::thread::spawn;
```

引入两个名字：

所以之后：

```rust
thread::sleep(...);
spawn(|| {
    println!("hello");
});
```

都可以使用。

等价于：

```
当前作用域：
    thread  ---> std::thread 模块
    spawn   ---> std::thread::spawn 函数
```

---

## 写法 2

```rust
use std::thread;
use thread::spawn;
```

这个也可以。

第一句：

```rust
use std::thread;
```

先把 `std::thread` 引入：

现在作用域里面有：

```rust
thread
```

然后第二句：

```rust
use thread::spawn;
```

这里的 `thread` 指的就是刚刚引入的模块。

所以相当于：

```rust
use std::thread::spawn;
```

最终效果一样。

---

不过注意一个细节：

`use` 是按照 **从上到下** 解析的。

例如：

```rust
use thread::spawn;
use std::thread;
```

通常会失败：

```
failed to resolve: use of unresolved module or unlinked crate \`thread\`
```

因为第一行执行时，当前作用域还没有 `thread` 这个名字。

---

## 写法 3

```rust
use std::thread::{self, spawn};
```

这是 Rust 的\*\*嵌套导入（nested imports）\*\*语法。

它等价于：

```rust
use std::thread;
use std::thread::spawn;
```

也就是：

```rust
{
    thread,
    spawn
}
```

意思是：

> 从 `std::thread` 里面同时导入 `thread` 自己和它里面的 `spawn` 。

---

## 实际区别

虽然三者最终都能写：

```rust
thread::sleep(Duration::from_secs(1));

spawn(|| {
    println!("hello");
});
```

但是推荐程度不同。

### 最推荐：写法 3

```rust
use std::thread::{self, spawn};
```

原因：

- 简洁
- 一眼看出两个东西来自同一个模块
- Rust 官方代码里经常这样写

例如：

例如：

```rust
use std::collections::{HashMap, HashSet};
```

就是同一种风格。

---

### 写法 1 也很常见

```rust
use std::thread;
use std::thread::spawn;
```

优点：

- 初学者容易理解
- Git diff 修改时更清晰

缺点：

- 重复写了 `std::thread`

---

### 写法 2 不太推荐

```rust
use std::thread;
use thread::spawn;
```

虽然合法，但可读性稍差。

原因是：

看到：

```rust
use thread::spawn;
```

读者不知道 `thread` 来自哪里：

可能是：

```rust
mod thread;
```

也可能是：

```rust
use xxx::thread;
```

必须往上看。

---

## 另外一个容易混淆的点

下面两个 **不是一回事** ：

### 导入模块

```rust
use std::thread;
```

之后：

```rust
thread::spawn(...)
```

---

### 导入函数

```rust
use std::thread::spawn;
```

之后：

```rust
spawn(...)
```

---

Rust 的 `use` 本质上只是给路径创建一个 **别名（alias）** 。

例如：

```rust
use std::thread::spawn;
```

相当于告诉编译器：

> 以后看到 `spawn` ，就把它理解成 `std::thread::spawn` 。

它不会复制函数，也不会改变函数的位置。

---

所以总结：

| 写法                                       | 等价 | 推荐     |
| ------------------------------------------ | ---- | -------- |
| `use std::thread; use std::thread::spawn;` | ✅   | ⭐⭐⭐   |
| `use std::thread; use thread::spawn;`      | ✅   | ⭐⭐     |
| `use std::thread::{self, spawn};`          | ✅   | ⭐⭐⭐⭐ |

日常 Rust 代码里，第三种通常是最符合 Rust 风格的写法。

---

# 通配符导入

第4种如下

```rust
use std::thread::*
```

前3种写法基本等价？那第4种写法是否等价？哪些写法会导入未使用的代码？

---

第 4 种：

```rust
use std::thread::*;
```

和前 3 种 **不完全等价** 。

它叫 **glob import（通配符导入）** ，意思是：

> 把 `std::thread` 模块中所有公开（ `pub` ）的成员全部导入当前作用域。

---

## 1\. 和前三种的区别

假设 `std::thread` 里面有：

```rust
pub fn spawn() {}
pub fn sleep() {}
pub struct Builder {}
pub struct JoinHandle {}
// ...
```

那么：

```rust
use std::thread::*;
```

相当于：

```rust
use std::thread::spawn;
use std::thread::sleep;
use std::thread::Builder;
use std::thread::JoinHandle;
// ...
```

而：

```rust
use std::thread::{self, spawn};
```

只相当于：

```rust
use std::thread;
use std::thread::spawn;
```

所以：

```rust
use std::thread::*;
```

导入的东西更多。

---

## 2\. self 和 \* 的区别

这是很多 Rust 初学者容易混淆的地方。

### {self, spawn}

```rust
use std::thread::{self, spawn};
```

表示：

```
std::thread
 ├── thread  ← 导入模块本身
 └── spawn   ← 导入成员
```

得到：

```rust
thread::sleep();
spawn();
```

---

### \*

```rust
use std::thread::*;
```

表示：

```
std::thread
 ├── spawn
 ├── sleep
 ├── Builder
 ├── JoinHandle
 └── 所有 pub 成员
```

得到：

```rust
spawn();
sleep();
Builder::new();
```

但是：

```rust
thread::sleep();
```

反而可能不行。

因为你 **没有导入 thread 这个名字本身** 。

---

例如：

```rust
use std::thread::*;

fn main() {
    sleep(std::time::Duration::from_secs(1));
}
```

可以。

但是：

```rust
use std::thread::*;

fn main() {
    thread::sleep(std::time::Duration::from_secs(1));
}
```

可能报错：

```
use of undeclared crate or module \`thread\`
```

因为 `thread` 这个名字没有被导入。

---

## 3\. 哪些会导入未使用代码？

这里需要区分：

### 写法 1

```rust
use std::thread;
use std::thread::spawn;
```

例如：

```rust
fn main() {
    spawn(|| {});
}
```

那么：

```rust
thread
```

没有使用。

编译器会警告：

```
warning: unused import: \`std::thread\`
```

---

### 写法 2

```rust
use std::thread;
use thread::spawn;
```

情况一样。

如果只用：

```rust
spawn(|| {});
```

那么：

```rust
thread
```

这个导入没有使用。

---

### 写法 3

```rust
use std::thread::{self, spawn};
```

也是一样。

如果：

```rust
spawn(|| {});
```

那么：

```rust
self
```

导入的 `thread` 没使用。

警告：

```
unused import: \`self\`
```

---

### 写法 4

```rust
use std::thread::*;
```

稍微特殊。

假设你只用：

```rust
spawn(|| {});
```

那么：

实际上只有：

```rust
spawn
```

被使用。

其它：

```rust
sleep
Builder
JoinHandle
...
```

虽然被导入，但 Rust **通常不会对 glob import 的每个未使用成员逐个报警** 。

否则：

```rust
use std::prelude::*;
```

这种大量通配符导入会产生海量警告。

所以：

```rust
use std::thread::*;
```

隐藏了大量未使用导入。

---

## 4\. 为什么 Rust 不推荐 \*？

例如：

```rust
use std::thread::*;
use std::sync::*;
```

如果两个模块都有：

```rust
Builder
```

可能产生名字冲突：

```
error[E0659]: \`Builder\` is ambiguous
```

因为编译器不知道：

```rust
Builder::new()
```

到底是哪一个。

---

所以 Rust 社区通常遵循：

### 推荐：

```rust
use std::thread::{self, spawn};
```

或者：

```rust
use std::thread::spawn;
```

### 不推荐：

```rust
use std::thread::*;
```

除非：

- 测试代码
- 小型示例
- 某些 trait 大量导入场景

---

# 总结表

| 写法                              | 导入范围      | 是否可能导入未使用代码 |
| --------------------------------- | ------------- | ---------------------- |
| `use std::thread;`                | 只导入模块    | 可能                   |
| `use std::thread::spawn;`         | 只导入函数    | 不会                   |
| `use std::thread::{self, spawn};` | 模块 + spawn  | 可能（self）           |
| `use std::thread::*;`             | 所有 pub 成员 | 大量，但通常不逐个警告 |

所以前三种的核心区别只是 **路径写法不同** ；第四种则是 **导入策略完全不同** 。
