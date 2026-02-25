### 动态数组的创建

```rust
let mut v = vec![2; 1024];
```

| **目标**     | **错误写法**        | **正确写法**                        |
| ---------- | --------------- | ------------------------------- |
| **重复初始值**  | `vec![2:1024]`  | `vec![2; 1024]`                 |
| **创建范围序列** | `vec![0..1024]` | `(0..1024).collect::<Vec<_>>()` |

### 深入理解迭代器模式

| **方法**               | **迭代器产出的内容** | **循环变量写法**         | **适用场景**         |
| ---------------------- | -------------------- | ------------------------ | -------------------- |
| `v.iter()`             | `&T` (引用)          | `for e in v.iter()`      | 只需读取值           |
| `v.iter().enumerate()` | `(usize, &T)`        | `for (i, e) in ...`      | **既要索引又要值**   |
| `v.iter_mut()`         | `&mut T` (可变引用)  | `for e in v.iter_mut()`  | 需要修改原值         |
| `v.into_iter()`        | `T` (所有权转移)     | `for e in v.into_iter()` | 消耗掉数组，不再使用 |

#### 只读元素

```rust
fn main() {
  let v=vec![1,2,3,4];
  for e in v.iter(){
      println!("{e}");
  }
}

/* 结果如下
    1
    2
    3
    4
 */
```

#### 修改元素

```rust
fn main() {
  let mut v=vec![1,2,3,4];
  for e in v.iter_mut(){
      *e = -*e;
      println!("{e}");
  }
}

/* 结果如下
    -1
    -2
    -3
    -4
 */
```

#### 只读取键值对

```rust
fn main() {
  let v=vec![1,2,3,4];
  for (i, e) in v.iter().enumerate(){
      println!("{i} => {e}");
  }
}

/* 结果如下
    0 => 1
    1 => 2
    2 => 3
    3 => 4
 */
```

#### 读写键值对

```rust
fn main() {
    let mut v = vec![1, 2, 3, 4]; // 1. 必须是 mut v
    for (i, e) in v.iter_mut().enumerate() { // 2. 使用 iter_mut()
        *e = -*e; // 3. 显式解引用并赋值
        println!("{i} => {e}"); // 4. 这里的 e 会自动解引用打印
    }
}

/* 结果如下
    0 => -1
    1 => -2
    2 => -3
    3 => -4
 */
```

### 模式匹配的写法

如果你觉得写 `*e` 很麻烦，Rust 提供了一种 **模式匹配（Pattern Matching）** 的优雅写法，在进入循环时就解开引用（多次出现 mut 反而不优雅）

```rust
fn main() {
  let mut v=vec![1,2,3,4];
  for &mut mut e in v.iter_mut(){
      e = -e;
      println!("{e}");
  }
}

/* 结果如下
    -1
    -2
    -3
    -4
 */
```

读写键值对的例子变成下面这样

```rust
fn main() {
    let mut v = vec![1, 2, 3, 4];
    for (i, &mut mut e) in v.iter_mut().enumerate() {
        e = -e;
        println!("{i} => {e}");
    }
}

/* 结果如下
    -1
    -2
    -3
    -4
 */
```

### 相关链接

[6 为啥要用 enumerate()](<6%20为啥要用%20enumerate().md>)
