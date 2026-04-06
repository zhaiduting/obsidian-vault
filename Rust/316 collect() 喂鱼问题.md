所谓的喂鱼，其实就是传递泛型参数。

### 投喂 `Vec`

```rust
let words = "hello world hello".split_whitespace();
let vector = words.collect::<Vec<&str>>();

println!("{:?}", vector);
// 输出 ["hello", "world", "hello"]
```

### 投喂 `HashSet`

```rust
use std::collections::HashSet;

let words = "hello world hello".split_whitespace();
let set = words.collect::<HashSet<&str>>();

println!("{:?}", set);
// 输出 {"world", "hello"} 或 {"hello", "world"}
```

### 懒人鱼

将泛型参数的泛參使用 `_` 替代后程序照样可以正常运行，这种偷懒的写法叫做「懒人鱼」。例如
`words.collect::<Vec<_>>()` 或者 `words.collect::<HashSet<_>>()`

### 所有权的问题

尝试将前两个示例合二为一，结果报错。

```rust
use std::collections::HashSet;

let words = "hello world hello".split_whitespace();

let vector = words.collect::<Vec<_>>();
let set = words.collect::<HashSet<_>>(); // ❌

println!("{:?}", vector);
println!("{:?}", set);
```

`split_whitespace` 返回一个迭代器，迭代器像是一个“一次性抽纸盒”。每抽出一张（执行一次 `next()`），盒子里的纸就少一张。`collect` 会不停地抽，直到抽空为止。

`collect` 函数的签名通常是 `fn collect<B>(self) -> B`。注意这里的 `self`，它没有 `&`。这意味着 `words` 这个变量的所有权被**移动**进了 `collect` 函数内部。一旦函数执行完，`words` 在当前作用域就失效了。

再次执行 `collect` 会报错 value used here after move（值在移动后被再次使用）。

为什么 `collect` 必须拿走 `self` 而不是借用它呢？这是因为迭代器的本质是一个「消耗性的状态机」

- **状态改变**：迭代器内部有一个指针（或者索引），每调用一次 `next()`，指针就往后移一位。
- **不可逆性**：如果你只传 `&self`（不可变引用），根据 Rust 的安全规则，`collect` 就不能修改迭代器内部的指针位置。
- **死循环风险**：如果 `collect` 不改变迭代器的状态，那它永远只能读到第一张“抽纸”，无法完成“收集全部”的任务。

如果 `collect` 设计成可变借用 `self` 会怎样？可以通过迭代器的 `by_ref` 方法进行模拟，因为它返回的就是迭代器的可变借用。代码如下

```rust
let mut words = "hello world".split_whitespace();

// 第一次调用 collect（通过可变引用）
// 这会把 words 里的指针推到最后
let v1: Vec<_> = words.by_ref().collect();
println!("第一次收集: {:?}", v1); // 输出: ["hello", "world"]

// 第二次调用 collect
// 此时 words 已经“干涸”了
use std::collections::HashSet;
let v2: HashSet<_> = words.collect();
println!("第二次收集: {:?}", v2); // 输出: {}
```

以上结果表明，再次调用 `collect` 只能得到一个空容器。因为首次执行 `collect` 后，指针已经走到头，东西已被取光了。

> [!TIP]
> `fn by_ref(&mut self) -> &mut Self` 是迭代器的一个“**变身术**”。它能把一个“**会消失的调用**”变成一个“**只借走的调用**”。

总之，无论是可变借用还是不可变借用都不能满足本例要求。正确方法是：克隆迭代器，然后对克隆版的迭代器与原版迭代器分别调用 `collect` 方法，结果互不影响。

```rust
let vector: Vec<_> = words.clone().collect(); // 克隆迭代器
let set: HashSet<_> = words.collect(); // ✅
```
