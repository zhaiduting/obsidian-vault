---
source: https://chat.deepseek.com/a/chat/s/8d2307ed-c655-4e9d-a1a0-44c909dc9a6d
author:
  - DeepSeek
created: 2025-05-19
tags:
  - clippings
---

在 Go 的 `fmt` 包中，打印函数确实有很多变体，但它们遵循一套清晰的命名规则，主要通过前缀和后缀来区分功能。以下是它们的核心区别和分类：

---

### 1\. 输出目标不同

#### (1) 标准输出（控制台）

- `Print` / `Println` / `Printf`  
    直接输出到标准输出（控制台）。

    ```go
    fmt.Print("Hello")       // 无换行
    fmt.Println("Hello")     // 自动追加换行
    fmt.Printf("%s", "Hello") // 格式化输出
    ```

#### (2) 字符串（返回 string）

- `Sprint` / `Sprintln` / `Sprintf`  
    将结果作为字符串返回（不输出到控制台）。

    ```go
    s := fmt.Sprintf("Name: %s", "Alice") // 返回格式化字符串
    ```

#### (3) 写入到 io.Writer

- `Fprint` / `Fprintln` / `Fprintf`  
    将结果写入到任意实现了 `io.Writer` 接口的对象（如文件、网络连接等）。

    ```go
    file, _ := os.Create("output.txt")
    fmt.Fprintf(file, "Data: %d", 42) // 写入文件
    ```

---

### 2\. 格式化与换行控制

- **`Print` / `Sprint` / `Fprint`**  
    无格式化，按原样输出，不自动换行。
- **`Println` / `Sprintln` / `Fprintln`**  
    在相邻参数间加空格，最后追加换行。

    ```go
    fmt.Println("Hello", "World") // 输出 "Hello World\n"
    ```

- **`Printf` / `Sprintf` / `Fprintf`**  
    支持格式化占位符（如 `%s` 、 `%d` ）。

    ```go
    fmt.Printf("%.2f", 3.14159) // 输出 "3.14"
    ```

---

### 3\. 特殊变体

- **`Errorf`**  
    格式化并返回一个 `error` 类型（本质是 `Sprintf` 的封装）。

    ```go
    err := fmt.Errorf("Error: %s", "file not found")
    ```

---

### 总结表格

| 函数       | 输出目标    | 格式化 | 换行 | 返回值       |
| ---------- | ----------- | ------ | ---- | ------------ |
| `Print`    | 标准输出    | 否     | 否   | \-           |
| `Println`  | 标准输出    | 否     | 是   | \-           |
| `Printf`   | 标准输出    | 是     | 否   | \-           |
| `Sprint`   | 字符串      | 否     | 否   | `string`     |
| `Sprintln` | 字符串      | 否     | 是   | `string`     |
| `Sprintf`  | 字符串      | 是     | 否   | `string`     |
| `Fprint`   | `io.Writer` | 否     | 否   | 写入的字节数 |
| `Fprintln` | `io.Writer` | 否     | 是   | 写入的字节数 |
| `Fprintf`  | `io.Writer` | 是     | 否   | 写入的字节数 |
| `Errorf`   | \-          | 是     | 否   | `error`      |

---

### 选择建议

- 需要调试输出到控制台？用 `Println` （简单）或 `Printf` （格式化）。
- 需要拼接字符串？用 `Sprintf` 。
- 需要写入文件或网络？用 `Fprintf` 。
- 需要返回错误？用 `Errorf` 。

掌握这些函数的命名规则后，基本可以举一反三，无需死记硬背！
