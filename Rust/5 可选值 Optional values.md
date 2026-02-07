Option 类型表示可选值：每个 Option 要么是 Some（包含一个值），要么是 None（不包含任何值）。Option 类型在 Rust 代码中非常常见，因为它们有很多用途：

- 初始值
- 偏函数的返回值
- 用于报告简单错误的返回值，错误时返回 None
- 可选的结构体字段
- 可以借用或“获取”的结构体字段
- 可选的函数参数
- 可空指针
- 用于在棘手情况下进行交换

Option 通常与模式匹配结合使用，用于查询值是否存在并执行相应的操作，始终会考虑 None 的情况。

### 1. Initial values (初始值)

用于表示一个变量在逻辑开始时还没有具体数值，稍后会被赋值。

```rust
let mut target_index: Option<usize> = None; // 初始状态为空

for (i, &val) in [1, 5, 10].iter().enumerate() {
    if val > 5 {
        target_index = Some(i); // 找到符合条件的索引后更新
        break;
    }
}

println!("{:?}",target_index); // Some(2)
println!("{}",target_index.unwrap()); // 2
```

iter() 迭代数组的每一个元素，拿到各元素的引用 `&1, &5, &10`；
enumerate() 逐条列出并编号为 `(index, value)` 格式的「元组」

```rust
(0, &1)
(1, &5)
(2, &10)
```

for 循环里找到目标索引并用 Some 包装后赋值给 target_index；
直接打印 target_index 会得到 Some(2)；想要打印 2 的话就要使用 `unwrap()` 解包装。

问题：[为啥要用 enumerate() 呢？不用行不？](<Rust/6%20为啥要用%20enumerate().md>)

### 2. Return values for partial functions (偏函数的返回值)

在数学中，函数通常被认为是**全函数（Total Function）**，即对于定义域（Domain）里的每一个输入，都有一个对应的输出。而偏函数（Partial Function）是指：在一个给定的输入集合中，只有**部分**输入能够产生输出，而另一部分输入会导致函数“无定义”或“无意义”。

在传统的编程语言（如 C 或 Java）中，如果一个函数遇到了它无法处理的输入，通常会返回 `null`、`-1` 或者直接抛出异常。Rust 是一门追求类型安全的语言，它通过 `Option<T>` 将“无定义”的情况显性化。

#### 除法函数

$f(a, b) = a / b$ 在实数范围内是一个典型的偏函数，因为当 $b = 0$ 时，该函数没有意义。

```rust
fn divide(numerator: f64, denominator: f64) -> Option<f64> {
    if denominator == 0.0 {
        // 对于输入 0.0，函数没有定义，所以返回 None
        None
    } else {
        // 对于其他输入，函数有定义，返回 Some
        Some(numerator / denominator)
    }
}
```

#### 对数函数

$\log(x)$ 仅在 $x > 0$ 时有定义。如果输入 $x \le 0$，它就是一个偏函数。

```rust
fn safe_log(x: f64) -> Option<f64> {
    if x <= 0.0 {
        None // 负数或零在实数对数中无定义
    } else {
        Some(x.ln()) // 计算以 e 为底的 x 的对数
    }
}
```

### 3. Reporting simple errors (报告简单错误)

当错误原因非常明显（如“未找到”），不需要详细的错误类型（如 `Result`）时，直接返回 `None`。

```rust
fn add_last_numbers(stack: &mut Vec<i32>) -> Option<i32> {
    Some(stack.pop()? + stack.pop()?)
}
```

更多示例以及问号语法相关内容： [问号运算符](5%20问号运算符.md)

### 4. Optional struct fields (可选的结构体字段)

用于定义某些属性并非所有对象都必须拥有的场景。

```rust
struct User {
    username: String,
    nickname: Option<String>, // 用户可以不设置昵称
}

let user = User {
    username: String::from("rust_ace"),
    nickname: None,
};
```

### 5. Struct fields that can be loaned or "taken" (可借用或“拿走”的字段)

在所有权操作中，有时需要从对象中临时取走一个值而不销毁对象。

```rust
#[derive(Debug)] // 必须加上这个，才能用 {:?} 打印

struct Container {
    data: Option<String>,
}

fn main() {
    let mut c = Container {
        data: Some("Important stuff".to_string())
    };

    println!("take 之前: {:?}", c);

    // 使用 take() 拿走所有权，原地留下 None
    let data = c.data.take();

    println!("take 之后: {:?}", c);
    println!("拿出来的 data: {:?}", data);
}
```

使用 [play.rust-lang.org](https://play.rust-lang.org/?version=stable&mode=debug&edition=2024) 在线执行，结果如下

```
take 之前: Container { data: Some("Important stuff") }
take 之后: Container { data: None }
拿出来的 data: Some("Important stuff")
```

### 6. Optional function arguments (可选的函数参数)

模拟其他语言中的默认参数或可选参数。

```rust
fn greet(name: Option<&str>) {
    match name {
        Some(n) => println!("Hello, {n}!"),
        None => println!("Hello, stranger!"),
    }
}

greet(Some("Ferris")); // Hello, Ferris!
greet(None);
```

### 7. Nullable pointers (可空指针)

在 Rust 中，引用（`&T`）永远不能为空。`Option<&T>` 是实现“安全的可空指针”的标准方式，且享有内存优化（空指针优化）。

以下代码展示 Rust 如何利用 `Option` 机制来实现**既能指向数据、又能表示“空”的逻辑，同时保持了类型安全**。

```rust
let x = 5;
let mut nullable_ptr: Option<&i32> = Some(&x); // 相当于有效指针

nullable_ptr = None; // 相当于 NULL 指针，但它是类型安全的
```

### 8. Swapping things out of difficult situations (在复杂情况下交换值)

在处理无法部分移动（partial move）的结构体或需要避免克隆时，利用 `Option` 配合 `mem::replace` 或 `take` 进行状态切换。

```rust
use std::mem;

#[derive(Debug)]
enum State {
    Working(String),
    _Idle,
}

let mut state = State::Working("Task 1".to_string());
// 当我们需要修改 Working 内部的值，但受限于所有权时
if let State::Working(name) = &mut state {
    let old_name = mem::replace(name, "Task 2".to_string());
    println!("old_name = {}", old_name);
    println!("name = {}", name);
}

println!("state = {:?}", state)
```

通过模式匹配，把枚举内部已有的 `String` 以 `&mut String` 的形式绑定到一个局部变量 `name` 上， 从而在这个作用域内读写它。运行结果如下：

```shell
old_name = Task 1
name = Task 2
state = Working("Task 2")
```
