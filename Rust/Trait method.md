---
source: https://gemini.google.com/app/3e32603da6ae22b3
---

在很多语言中 `obj.method` 或 `ClassName::method` 是调用某个方法的常见写法，Rust 也是这样。除此之外，Rust 还可以直接通过 `TraitName::method` 调用方法。直接通过接口名称调用方法，这在其它语言中很少见到。

---

### 关键

在 Rust 编译器的眼里，`TraitName::method()` 是一个**带洞的方程**。这个洞就是 `Self`。只要你能给出一个证据（不管是返回值、参数还是显式类型标注）把这个洞填上，它就能顺藤摸瓜找到对应的 `impl` 块。

以 `From` 为例，看看 `Self` 是如何被“逼”出来的：

```rust
// Trait 定义
trait From<T> {
    fn from(T) -> Self;
}

// 调用处
let s: String = From::from("hello");
```

- **第一步**：`"hello"` 的类型是 `&str`。所以 `From<T>` 中的 `T` 确定为 `&str`。
- **第二步**：左侧变量 `s` 的类型是 `String`。因为函数返回 `Self`，所以 `Self` 确定为 `String`。
- **第三步**：编译器现在拥有了完整信息：**`Self = String`, `T = &str`**。
- **第四步**：它去查找 `impl From<&str> for String`。找到了！调用成功。

**反例**：如果你写 `let _ = From::from("hello");`（不给左侧变量类型），编译器就会罢工，因为它不知道你想把 `&str` 转成什么。

---

### 例外

有些 Trait 方法既不接收 `Self` 也不返回 `Self`（比如一些配置类的关联函数）：

```rust
trait Config {
    fn default_port() -> u16; // 签名里完全没有 Self
}
```

这种情况下，你**永远无法**通过 `Config::default_port()` 调用。因为无论你把结果赋值给谁，`Self` 始终是悬空的。你必须显式指定类型（假设 Dog 实现了 Config 特征）：

- `Dog::default_port()`
- `<Dog as Config>::default_port()`

---

### 总结 Self 的角色

在 Rust 中，`Self` 不仅仅是一个类型，它是一个**索引**：

| **情况**                            | **编译器状态** | **结果**                        |
| ----------------------------------- | -------------- | ------------------------------- |
| **`Self` 唯一确定**                 | 索引成功       | 调用具体的 `impl` 代码          |
| **`Self` 不确定（多个 impl 匹配）** | 索引模糊       | 报错：`type annotations needed` |
| **`Self` 无法推导（签名中未出现）** | 索引缺失       | 报错：需使用完全限定语法        |

---

### 完整示例 1

```rust
trait Parser {
    // 关联函数，返回 Self
    fn parse(s: &str) -> Self;
}

struct Number(i32);
struct Word(String);

impl Parser for Number {
    fn parse(s: &str) -> Self {
        Self(s.parse().unwrap_or(0))
    }
}

impl Parser for Word {
    fn parse(s: &str) -> Self {
        Self(s.to_string())
    }
}

fn main() {
    // 场景 A：通过左侧类型推导 Self
    // 编译器看到 : Number，于是将 Parser::parse 替换为 <Number as Parser>::parse
    let n: Number = Parser::parse("123");
    let w: Word = Parser::parse("123");
    // let s: String= Parser::parse("123"); // ❌ 并没有为 String 实现 Parser

    // 场景 B：通过函数参数推导 Self
    fn consume_word(w: Word) { println!("Got a word"); }
    // 编译器看到函数需要 Word，推导出 Self = Word
    consume_word(Parser::parse("hello"));

    // 场景 C：推导失败
    // let x = Parser::parse("???");
    // ^--- 报错：type annotations needed。编译器：我不知道你想解析成 Number 还是 Word！
}
```

### 完整示例 2

以下 `Worker::recruit()` 调用成功 ✅，而 `Worker::company_name()` 却会失败 ❌

```rust
use std::fmt::Display;

trait Worker {
    // 1. 返回 Self 的关联函数
    fn recruit() -> Self;
    // 2. 不返回 Self 的关联函数
    fn company_name() -> String;
    // 3. 实例方法
    fn work(&self);
    // 4. 泛型关联函数
    fn produce<T: Display>(item: T) -> String;
}

struct Robot {}
impl Worker for Robot {
    fn recruit() -> Self { Self {} }
    fn company_name() -> String { "Cyberdyne Systems".to_string() }
    fn work(&self) { println!("Robot is working..."); }
    fn produce<T: Display>(item: T) -> String { format!("Robot produced: {}", item) }
}

fn main() {
    // --- 形式 1: 依赖返回值推导 Self ---
    // 就像 From::from，因为左侧标注了 Robot，编译器知道 Self = Robot
    let bot: Robot = Worker::recruit(); // ✅

    // --- 形式 2: 通过类型直接调用 ---
    // 这是调用关联函数最常用的方式
    let name = Robot::company_name();
    // Worker::company_name(); // ❌

    // --- 形式 3: 经典的实例调用 (点语法) ---
    // 编译器会自动处理 &self 的借用
    bot.work();

    // --- 形式 4: 涡轮鱼 (Turbofish) 处理泛型 ---
    // 既要确定 Self (通过 Robot::)，又要确定 T (通过 ::<i32>)
    let result = Robot::produce::<i32>(42);

    // --- 形式 5: 完全限定语法 (消除歧义的神器) ---
    // 如果有多个 Trait 都有 company_name，这是唯一解
    let specific_name = <Robot as Worker>::company_name();

    println!("Company: {}, Result: {}", name, result);
}
```

---

### 💡 进阶

有些时候，即使你确定了 `Self`，如果方法还有额外的泛型参数 `T`，你还得用 **Turbofish (`::<>`)**。

- `Self` 决定了**谁的代码**被执行（哪个 `impl`）。
- `T` 决定了这部分代码**如何处理不同的类型**。
