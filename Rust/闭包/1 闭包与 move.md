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

### move 的本质

[闭包的函数特征](1%20闭包的函数特征.md)文末有一段描述变量捕获法则的伪代码，对于使用了 move 修饰的闭包，应该改成如下形式

```rust
if move {
    捕获 T
} else if 只读 {
    捕获 &T
} else if 需要修改 {
    捕获 &mut T
} else {
    捕获 T
}
```

考虑到多变量的捕获情况，改进的伪代码类似如下

```rust
for v in 捕获变量 {
    if move {
        capture(v, T)
    } else {
        if 只读 {
            capture(v, &T)
        } else if 修改 {
            capture(v, &mut T)
        } else {
            capture(v, T)
        }
    }
}
```

> [!TIP]
> 给闭包添加 move 修饰符，不会更改闭包原本实现的函数特征 ✅

核心原因一句话：

> 👉 **Fn / FnMut / FnOnce 只取决于“闭包体内对捕获变量的使用方式”，而不是捕获方式本身**

而：

- `move` **只改变捕获方式**（`&T / &mut T → T`）
- **不改变使用方式**（读 / 改 / 消费）

> [!TIP]
> 没有 move 修饰的闭包，捕获的变量全是借用 ❌

以下闭包没有 move 修饰，但是变量 world 的捕获方式并不是借用

```rust
let world = "world".to_string();
let f = || {
	let move_world = world;
};
```
