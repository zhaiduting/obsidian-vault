### enumerate() 版

enumerate `/ɪ'njuːməreɪt/` *vt.* 列举，枚举；数
iterate `/'ɪtəreɪt/` vt. 反复说；重复；重做；[计]迭代

```rust
let mut target_index: Option<usize> = None;

for (i, &val) in [1, 5, 10].iter().enumerate() {
    if val > 5 {
        target_index = Some(i);
        break;
    }
}

println!("{:?}",target_index);
```

### 无 enumerate()

```rust
let mut target_index: Option<usize> = None;
let mut i = 0;
for val in [1, 5, 10].iter() {
    if *val > 5 {
        target_index = Some(i);
        break;
    }
    i +=1;
}

println!("{:?}",target_index);
```

### 结论

不用 `enumerate()` 也行，但是 i 需要手动维护，潜在问题如下

- 忘了写 `i += 1`
- `i += 1` 写错了位置
- 修改循环逻辑后漏改 `i`

使用 `enumerate()` 后，可以完全删除对索引递增的手动维护。索引 `i` 与元素 `val` 来源于同一个迭代器抽象，由 Rust 在内部自动维护。这种写法减少了出错空间，更符合 Rust 的惯用风格（idiomatic Rust）。

### 注意

即使数组很大，调用 `enumerate()` 也不会影响效率。这与它的实现原理有关。
