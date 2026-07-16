---
source: https://kaisery.github.io/trpl-zh-cn/ch15-03-drop.html
---

Drop 特征有点类似于 C 里面的析构函数

```rust
struct CustomSmartPointer {
    data: String,
}
impl Drop for CustomSmartPointer {
    fn drop(&mut self) {
        println!("Dropping {}", self.data);
    }
}
fn main() {
    {
        let c = CustomSmartPointer { data: String::from("stuff 1") };
    }
    let d = CustomSmartPointer { data: String::from("stuff 2") };
    let e = CustomSmartPointer { data: String::from("stuff 3") };
    println!("----------------");
    let f = CustomSmartPointer { data: String::from("stuff 4") };
    println!("{}", e.data);
    drop(e);
    // println!("{}", e.data);
}
```

运行结果如下

```sh
Dropping stuff 1
----------------
stuff 3
Dropping stuff 3
Dropping stuff 4
Dropping stuff 2
```

其中 c 超出作用域自动执行 c.drop
而 drop(e) 是手动执行的（不允许手动执行 e.drop ）
当 main 函数执行完毕准备退出时，自动执行 d 和 f 的清理函数（逆序）
