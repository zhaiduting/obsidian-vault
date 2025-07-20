---
author:
  - DeepSeek
---

在 Go 语言中，`for rows.Next()`  这种循环模式是用于迭代数据库查询结果的常见方式，它与  `for k, v := range array`  这种 range 循环有本质区别。下面解释它是如何工作的：

### 工作原理：

1.  **`rows.Next()`  方法**：

    - 该方法准备下一行数据以供读取
    - 如果成功返回  `true`，如果已经没有更多行或发生错误则返回  `false`
    - 每次调用都会推进游标到下一行

2.  **`rows.Scan()`  方法**：

    - 当  `Next()`  返回 true 时，你可以调用  `Scan()`  将当前行的列值读取到你提供的变量中
    - 你需要预先声明变量（如例子中的  `id`, `name`, `age`）
    - 通过传递指针（`&id`, `&name`, `&age`）让 Scan 方法能够填充这些变量

3.  **循环流程**：

```go
for rows.Next() { // 1. 检查是否有下一行
    // 2. 声明变量
    var id int
    var name string
    var age int

    // 3. 扫描当前行数据到变量
    if err := rows.Scan(&id, &name, &age); err != nil {
        log.Fatal(err)
    }

    // 4. 使用被填充的变量
    fmt.Printf("ID: %d, Name: %s, Age: %d\n", id, name, age)
 }
```

### 与 range 循环的关键区别：

1. **数据获取方式不同**：

    - range 循环自动解包迭代元素到变量
    - rows.Next() 需要手动 Scan 数据到变量

2. **变量作用域**：

    - 在 rows.Next() 循环中，变量通常在循环体内声明，每次迭代都是新的变量
    - range 循环的 k,v 变量是每次迭代重用

3. **底层机制**：

    - rows.Next() 是数据库驱动的低级接口，需要处理可能出现的错误
    - range 是语言内置的高级抽象

### 为什么这样设计？

数据库行扫描采用这种模式是因为：

1. 需要处理可能出现的错误
2. 允许灵活地处理不同列数和类型
3. 更高效的内存管理（可以重用变量）
4. 与数据库驱动的实现方式相匹配

这种模式在 Go 的标准库中很常见，比如  `bufio.Scanner`  也使用类似的  `Scan()` + `Text()`/`Bytes()`  方式迭代数据。
