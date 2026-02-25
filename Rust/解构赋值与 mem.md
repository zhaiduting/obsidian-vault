### 结构体示例

```rust
use std::mem;
#[derive(Debug)]
struct Point {
    x: isize,
    y: isize,
}
fn main() {
    let mut p = Point { x: 3, y: 4 };
    p.x = 33;
    p.y = 44;
    println!("{:?}", p);
    {
        let Point { x, .. } = &mut p;
        let _ = mem::replace(x, 3333);
    }
    println!("{p:?}");
}

/* 输出
Point { x: 33, y: 44 }
Point { x: 3333, y: 44 }
 */
```

`let Point { x, .. } = &mut p;` 从 p 中结构出 x 并且忽略忽略 y  
`let _ = mem::replace(x, 3333);` 用 3333 替换 x 中的旧值并且忽略旧值  
以上两行处于一个单独的作用域，否则之后的 `println` 会报错。
