仍以笔记[特征对象](1%20特征对象.md)中的代码为例。其中 `dyn Speaker` 是一个**类型**（Type），就像 `i32` 是一种类型一样。只不过 `i32` 是固定大小的，而 `dyn Speaker` 的大小取决于它背后具体的实现类，所以它是 **DST**（Dynamically Sized Type）。

### DST 赋值问题

特征对象类型必须通过指针（如 `&dyn Speaker`、`Box<dyn Speaker>`）使用。在赋值语句中，不能把具体类型直接赋值给特征对象类型。

```rust
// ❌ 不能直接使用 dyn Animal（它是 DST，大小未知）
// let animal: dyn Speaker = Dog;  // 编译错误！

// ✅ 特征对象必须通过指针使用
let animal: &dyn Speaker = &Dog;   // &dyn Animal 是特征对象
let boxed: Box<dyn Speaker> = Box::new(Dog);  // Box<dyn Animal> 是特征对象
```

### DST 分类

Rust 中主要的 DST 有以下几类：

#### 1. **切片（Slice）**

```rust
// 裸切片类型（大小未知）
let slice: [i32] = *vec;  // 不能直接使用，需通过引用
let ref_slice: &[i32] = &[1, 2, 3];  // 通过指针使用
let str_slice: &str = "hello";  // &str 是切片引用
```

#### 2. **特征对象（Trait Object）**

rust

trait Draw { fn draw(&self); }
let obj: &dyn Draw = &circle; // 你已经熟悉的

#### 3. **结构体最后一个字段是 DST（自定义 DST）**

```rust
// 最后一个字段是切片
struct MyStruct {
    count: usize,
    data: [i32],  // DST 字段必须在最后
}

// 最后一个字段是特征对象
struct Wrapper {
    name: String,
    obj: dyn Draw,  // DST 字段
}

// 通过指针使用
let boxed: Box<MyStruct> = /* ... */;
```

#### 4. **`std::ffi::CStr`**

```rust
use std::ffi::CStr;
// CStr 表示 C 字符串，大小动态
let c_str: &CStr = CStr::from_bytes_with_nul(b"hello\0").unwrap();
```

#### 5. **`std::marker::PhantomPinned` 相关类型**（较少见）

### 重要特征

所有 DST 的共同特点：

- **编译期大小未知**，无法直接按值使用
- **必须通过指针**（`&`、`Box`、`Rc` 等）访问
- 指针是**胖指针**（包含数据地址 + 额外元数据）
  - 切片：数据地址 + 长度
  - 特征对象：数据地址 + vtable 指针
