以下 set 方法报错，因为对于只读的 self 无法修改其属性。

将 set 方法改成 `fn set(&mut self, v: i64)` 可以解决这个问题。

```rust
struct Test {
    data: i64,
}
impl Test {
    fn set(&self, v: i64) {
        self.data = v
    }
}
```

同理，以下代码也会报错。因为 `let t` 声明的也是一个不可变绑定，而 set 方法需要绑定一个可变结构体。

解决方法是使用 `let mut t`

```rust
fn main() {
    let t = Test { data: 0 };
    t.set(10);
}
```

如果坚持使用 `let t` 该怎么做？可以使用 Cell 实现目标，如下所示

```rust
use std::cell::Cell;

struct Test {
    data: Cell<i64>,
}
impl Test {
    fn set(&self, v: i64) {
        self.data.set(v);
    }
}

fn main() {
    let t = Test { data: Cell::new(0) };
    t.set(10);
    println!("{}", t.data.get());
}
```

注意：

- `main` 函数中定义 t 时不需要 `mut`
- `set` 方法绑定 `self` 时也不需要 `mut`
- `std::cell::Cell` 解决了只读对象的属性修改问题。
