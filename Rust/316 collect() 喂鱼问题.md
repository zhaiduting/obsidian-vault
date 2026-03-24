所谓的喂鱼，其实就是传递范型参数。

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

将范型参数的参数使用 `_` 替代后程序照样可以正常运行，这种偷懒的写法叫做「懒人鱼」。例如
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

`collect` 函数的签名通常是 `fn collect<B>(self) -> B`。注意那个 `self`，它没有 `&`。这意味着 `words` 这个变量的所有权被**移动**进了 `collect` 函数内部。一旦函数执行完，`words` 在当前作用域就失效了。

再次执行 `collect` 会报错 value used here after move（值在移动后被再次使用）。

为什么 `collect` 必须拿走 `self` 而不是借用它呢？这是因为迭代器的本质是一个「消耗性的状态机」

- **状态改变**：迭代器内部有一个指针（或者索引），每调用一次 `next()`，指针就往后移一位。
- **不可逆性**：如果你只传 `&self`（不可变引用），根据 Rust 的安全规则，`collect` 就不能修改迭代器内部的指针位置。
- **死循环风险**：如果 `collect` 不改变迭代器的状态，那它永远只能读到第一张“抽纸”，无法完成“收集全部”的任务。

`collect` 能否可变借用 `self` 呢？也是无意义的，因为首次执行 `collect` 后指针已经走到头了，再次调用 `collect` 则不会有任何结果。

解决方法如下：克隆一个迭代器。先使用克隆版的迭代器进行搜集，后搜集的则不受影响。

```rust
let vector = words.clone().collect::<Vec<_>>(); // 克隆迭代器
let set = words.collect::<HashSet<_>>(); // 正常运行
```
