```go
func work() {
	ticker := time.NewTicker(time.Millisecond * 500)
	defer ticker.Stop() // 退出函数前停止 ticker
	for {
	    //time.Sleep(time.Millisecond * 100)
	    select {
	    case <-ticker.C:
	       fmt.Println(time.Now())
	    //default:
	    // fmt.Println("空")
	    }
	}
}
```

`ticker.C` 是 `time.Ticker` 结构体的一个成员，它是一个只读的 channel (`<-chan time.Time`)。`time.NewTicker(d)` 会创建一个新的 ticker，它会以固定的时间间隔 `d` 向 `ticker.C` 发送一个 time.Time 值。

在这个例子中，`ticker := time.NewTicker(time.Millisecond * 500)` 创建了一个每 0.5 秒发送一次 time.Time 值的 ticker。因此，`<-ticker.C` 这个操作会阻塞，直到 0.5 秒过去，ticker 向 `ticker.C` 发送了一个值，然后该操作会接收到这个值并继续执行。

如果启用 default 则会导致持续打印“空”

如果启用 default 并启用 time.Sleep 则会显示 4 次“空”和 1 次当前时间，然后再显示 4 空、1 时间，如此交替

### 注意

在 Go 1.23 版本之前，如果 `main` 函数退出，但程序中没有显式调用 `ticker.Stop()`，确实会造成内存泄漏。这是因为 `time.Ticker` 底层会有一个 goroutine 一直在运行，即使 `main` 函数退出了，这个 goroutine 仍然会尝试向 `ticker.C` 发送数据，而由于没有接收者，这个 goroutine 就无法结束，从而导致内存泄漏。

但是，Go 1.23 版本对 `time.Ticker` 和 `time.Timer` 进行了重要的改进，解决了这个问题。**Go 1.23 及以后的版本，即使没有调用 `ticker.Stop()`，只要程序中没有任何地方再引用这个 `Ticker` 对象，垃圾回收器就能够回收它，从而避免内存泄漏。**
