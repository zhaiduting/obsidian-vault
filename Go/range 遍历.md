### 遍历字符串

```go
for i, ch := range "你好Go" {
    fmt.Printf("index %d, rune %q\n", i, ch)
}

// index 0, rune '你'
// index 3, rune '好'
// index 6, rune 'G'
// index 7, rune 'o'
// 注意 index 并非连续的 0 1 2 3
```

- `i` 是字节位置（不是字符位置）
- `ch` 是字符的 Unicode 码点（`rune` 类型）

在 Go 语言中，`rune`  是  `int32`  的别名，表示一个 32 位的有符号整数（范围是  `-2147483648`  到  `2147483647`），用于表示 Unicode 码点（Unicode code point）。

### 遍历数组或切片

```go
nums := []int{2, 3, 4}
sum := 0
for _, num := range nums {
    sum += num
}
fmt.Println("sum:", sum) // sum: 9
```

- `range nums` 会返回两个值：
    - 第一个是索引（`i`）
    - 第二个是对应索引的值（`num`）
- `_` 是占位符，表示忽略索引

### 遍历 map

```go
m := map[string]string{"a": "apple", "b": "banana"}
for key, value := range m {
    fmt.Println(key, value)
}
// b banana
// a apple
// 注意 map 是无序的
```

### 遍历通道

```go
ch := make(chan int, 3)
ch <- 1
ch <- 2
ch <- 3
close(ch)

for val := range ch {
    fmt.Println(val)
}
// 1
// 2
// 3
```

`range ch`  会持续从通道读取值，直到通道被关闭  **并且**  所有缓冲的数据都被读取完毕。示例代码执行流程如下

1. `ch`  被缓冲 3 个值  `1, 2, 3`。
2. `close(ch)`  关闭通道，但  `1, 2, 3`  仍然在缓冲区内。
3. `for val := range ch`  会依次读取  `1, 2, 3`，然后检测到通道已关闭且无剩余数据，循环结束。

总之，关闭通道后仍然可以读取剩余数据，`range`  会读完所有值后安全退出。

如果通道未关闭，`range`  会一直等待新数据，可能导致死锁（除非有其他 goroutine 继续发送数据）。
