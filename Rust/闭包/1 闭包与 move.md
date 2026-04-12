关键字 `move` 是用来控制变量的捕获方式的，只能用于：

- 闭包
- async 块

注意 `move` 不能用于函数，类似于 `move fn foo(){}` 这样的写法是错误的。[闭包与函数的区别](0%20闭包与函数的区别.md)中提到过函数不能访问外部变量，因此函数也不存在变量的捕获方式问题。

### 基本作用

```rust
let data = vec![1, 2, 3];
let closure = move || println!("captured {data:?} by value");
// println!("{data:?}"); // error[E0382]: borrow of moved value: `data`
```