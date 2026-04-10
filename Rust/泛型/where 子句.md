先定义一个带关联类型的 Task 特征

```rust
trait Task {
    type Output; // Trait 定义了关联类型
}
```

再定义一个 start 函数，参数必须实现 Output 为 String 的 Task 特征

```rust
fn start<T: Task<Output = String>>(task: T) {
    println!("任务启动...");
}
```

上面的写法缺点是尖括号里面再套尖括号，不好看，可以改用等价的 where 写法优化一下。方法很简单，直接将「特征约束」从「函数签名」里移到「where 子句」就行了，如下：

```rust
fn start<T>(task: T)
where
    T: Task<Output = String>
{
    println!("任务启动...");
}
```

澄清一个概念，等式约束指的是 `Output = String`，它是特征约束的一部分。

在这个简单的例子里，where 写法的优势并不明显。但在以下三种情况下，`where` 写法会比 `inline` 写法（写在 `<>` 里）清晰得多：

#### 1. 当约束非常多时

如果一个泛型需要满足多个 Trait，或者有多个泛型参数，`inline` 写法会让函数名变得非常长，难以阅读。

Rust

```rust
// ❌ 乱成一团的 inline 写法
fn complex_start<T: Task<Output = String> + Clone + Send + Sync, U: Display>(task: T, data: U) { ... }

// ✅ 清爽的 where 写法
fn complex_start<T, U>(task: T, data: U)
where
    T: Task<Output = String> + Clone + Send + Sync,
    U: Display,
{
    // ...
}
```

#### 2. 当约束涉及到复杂的关联类型时

有时候你需要约束一个关联类型的关联类型，这种嵌套只有 `where` 能优雅处理。

#### 3. 语义化更强

`where` 子句将 **“声明泛型 T”** 与 **“对 T 的约束”** 分开了。函数名部分只负责定义有哪些参数，而逻辑上的“合同要求”全部由 `where` 负责。

---

### 总结对照

| **语法位置**      | **形式**                   | **推荐场景**                             |
| ----------------- | -------------------------- | ---------------------------------------- |
| **Inline (内联)** | `<T: Trait<Item = X>>`     | 只有 1 个简单约束时，快捷省事            |
| **Where 子句**    | `where T: Trait<Item = X>` | **多数情况下的首选**，尤其是约束变复杂时 |
