```rust
pub trait Summary {
    fn summarize(&self) -> String;
}
```

### 参数类型不能是纯 trait

下面的写法是错误的

```rust
pub fn notify(item: Summary) {todo!()} // ❌
```

### 静态分发的写法

指定 `impl` 关键字和 trait 名称

```rust
pub fn notify(item: &impl Summary) { // ✅
    todo!()
}
```

### 动态分发的写法

将 `impl` 改成 `dyn` 就得到了动态分发的写法

```rust
pub fn notify(item: &dyn Summary) { // ✅ 也对
    todo!()
}
```

### `impl` (静态) vs `dyn` (动态) 的区别：

| **特性**       | **&impl Summary (静态分发)**   | **&dyn Summary (动态分发)**        |
| -------------- | ------------------------------ | ---------------------------------- |
| **编译速度**   | 较慢（需要为每个类型生成代码） | 较快（只生成一份代码）             |
| **运行速度**   | **极快**（像直接调用函数一样） | 略慢（需要查表找方法地址）         |
| **二进制大小** | 较大（代码膨胀）               | 较小                               |
| **灵活性**     | 一个集合只能存同一种具体类型   | 一个集合可以存放多种不同的实现类型 |

**不能省略 `impl` 或 `dyn`：** 因为 Rust 需要明确知道你是要在编译时确定类型（静态），还是在运行时处理类型（动态）。

**必须有指针/引用：** 因为 Trait 描述的对象大小不一，必须通过固定大小的指针（如 `&` 或 `Box`）来引用它们。

### 完整代码

```rust
pub trait Summary {
    fn summarize(&self) -> String;
}

pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {} ({})", self.headline, self.author, self.location)
    }
}

pub struct SocialPost {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub repost: bool,
}

impl Summary for SocialPost {
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}

// pub fn notify(item: Summary) { todo!() } // ❌
pub fn notify1(item: &impl Summary) { // ✅
    println!("Breaking news! {}", item.summarize());
}
pub fn notify3(item: &dyn Summary) { // ✅ 也对
    println!("Breaking news!! {}", item.summarize());
}

fn main() {
    let post = SocialPost {
        username: String::from("horse_ebooks"),
        content: String::from(
            "of course, as you probably already know, people",
        ),
        reply: false,
        repost: false,
    };
    notify1(&post);
    notify3(&post);
}
```

运行结果如下

```
Breaking news! horse_ebooks: of course, as you probably already know, people
Breaking news!! horse_ebooks: of course, as you probably already know, people
```
