```rust
fn parse_numbers(strings: Vec<&str>) -> Result<Vec<i32>, ParseIntError> {
    strings
        .into_iter()
        .map(|s| s.parse::<i32>())
        .collect()
}
```

## strings 是怎么变成 Result 的？

### 第一步：`.into_iter()` —— 产生原始物料

它把 `Vec<&str>` 变成了迭代器，此时每一个元素都是 `&str`。

### 第二步：`.map(|s| s.parse::<i32>())` —— 包装成 Result

这一步非常关键。`s.parse::<i32>()` 的返回值本身就是 `Result<i32, ParseIntError>`。

- 所以，此时迭代器里流淌的不再是字符串，而是一连串的 **Result 盒子**。
- 状态：`Iterator<Item = Result<i32, E>>`

### 第三步：`.collect()` —— 魔法合体

这是所有疑惑的根源。`collect` 是一个泛型函数，它的行为完全取决于你**期望它返回什么**。

你在函数签名里写了：`-> Result<Vec<i32>, E>`。

1. `collect` 看到你的要求后，心领神会：“哦！你不是想要把这一堆 Result 收集成一个 `Vec<Result>`，而是想要一个**总的 Result**。”
2. 于是它开始“抽奖”：
   - 它每从迭代器里拿出一个元素（即一个 Result 盒子），就立刻打开看。
   - 如果是 `Ok(value)`，它就把 `value` 存进一个隐形的 `Vec` 里，然后继续拿下一个。
   - **转折点**：如果它一旦碰到一个 `Err(e)`，它会立即停下手里的活，把刚才攒下的 `Vec` 全扔了，直接返回 `Err(e)`。

3. 如果全部拿完了都是 `Ok`，它才把攒好的 `Vec` 装进 `Ok` 盒子里还给你。

## 形象化理解：传送带

1. **Map 阶段**：传送带上原本是“零件”（字符串），经过加工变成了“带包装的零件”（`Result` 盒子）。
2. **Collect 阶段**：
   - 如果你准备了一个 **`Vec<Result>`** 容器：传送带把盒子一个个原封不动装进大箱子。
   - 如果你准备了一个 **`Result<Vec>`** 容器：收货员会逐个拆盒。只要有一个盒子里是“炸弹”（`Err`），他就拒收整批货，直接给你一个报错单；只有全是“宝物”（`Ok`），他才给你一整箱宝物。

## 为什么在代码里看不见“转换逻辑”？

因为这个逻辑被**隐藏在 `Result` 对 `FromIterator` 特征的实现中**。

如果你手动写出这个逻辑，其实就是我们在前面提到的 `match` 循环：

```rust
let mut container = Vec::new();
for item in iterator {
    match item {
        Ok(val) => container.push(val),
        Err(e) => return Err(e), // 这里就是“全无”的体现
    }
}
Ok(container)
```

`collect()` 只是把上面这堆啰嗦的代码，通过**类型推导**压缩成了一个单词。
