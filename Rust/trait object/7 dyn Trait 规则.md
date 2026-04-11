判断 trait 能不能 `dyn` 必须满足：

- 方法不能返回 `Self`
- 方法不能是泛型
- 方法必须依赖 `self`（小写）

### 代码对比

```rust
trait Good {
    fn foo(&self);
    fn bar(&self) -> i32;
    fn baz(&self) -> Box<dyn Good>;
}

trait Bad {
    // 启用以下任意一行都会报错
    // fn foo();
    // fn bar<T>(&self, t: T) -> i32;
    // fn baz(&self) -> Self;
}

fn call(a: &dyn Good, b: &dyn Bad) {
    todo!()
}
```

### 最本质的一句话

> 👉 **trait object 的本质 = “一张固定函数表（vtable）”**

所以：

> ❗ 任何让“函数签名不确定”的设计，都会破坏它

也就是说：

- 函数参数类型 必须确定
- 返回值类型 必须确定
- 不能依赖 “具体类型是谁”

### 关联类型

怎么解决特征方法不能使用泛型的问题？可以使用关联类型替代泛型，将泛型方法转化为普通方法。

```rust
trait Worker {
    type Job; // 关联类型
    fn work(&self, t: Self::Job) -> i32;
}

fn start(c: &dyn Worker<Job=String>) { // ✅ dyn 正常
    todo!()
}
```
