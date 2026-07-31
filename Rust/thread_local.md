使用 `thread_local!` 宏可以创建**线程局部**变量。不同线程内的同名变量彼此孤立、互不影响，这点可以通过以下代码验证

```rust
use std::cell::RefCell;
use std::thread::{self, spawn};

thread_local! {
    static COUNTER:RefCell<u32> = RefCell::new(0);
}
fn add() {
    COUNTER.with(|c| { *c.borrow_mut() += 1; })
}
fn show() {
    COUNTER.with(|c| { println!("{:?}: {}", thread::current().id(), c.borrow()); })
}
fn spa() {
    spawn(|| {
        add();
        show()
    });
}
fn main() {
    add();
    show();
    add();
    show();
    spa();
    spa(); // 如果主线程结束较早，可能来不及看到 ThreadId(3) 甚至 ThreadId(2)
}
```

多次执行，可以看到类似如下结果

```sh
ThreadId(1): 1
ThreadId(1): 2
ThreadId(2): 1
ThreadId(3): 1
```

其中 main 函数中第2次的 show 显示计数器为 2，说明同线程内的变量与单线程类似。但是3个线程内的 COUNTER 彼此之间相互隔离，任何一个线程的 `COUNTER + 1 `不会影响到另一个线程
