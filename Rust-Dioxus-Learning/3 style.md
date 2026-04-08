先看错误写法

```rust
h3 {
    style { color: "red" }, // ❌ 这里被解析成了一个名为 <style> 的子标签
    "1. 简单数值 (i32)"
}
```

这在渲染时会变成 `<h3><style>color:red</style>1. 简单数值</h3>` 导致样式无效。

正确写法

```rust
h3 {
    color: "red", // ✅ 直接作为属性写在里面
    "1. 简单数值 (i32)"
}
```

更多样式

```rust
h3 {
    style: "color: red; font-style: italic;", // 像 HTML 一样写字符串
    "1. 简单数值 (i32)"
}
```

使用 class

```rust
h3 {
    class: "my-title",
    "1. 简单数值 (i32)"
}
```
