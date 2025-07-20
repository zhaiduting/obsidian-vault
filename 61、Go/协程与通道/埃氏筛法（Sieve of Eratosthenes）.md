埃拉托斯特尼筛法，难懂得一逼！以下代码使用 ✅ 标记了 50 以内的质数以及第一个大于 50 的质数（53）

```go
// Copyright 2009 The Go Authors. All rights reserved.  
// Use of this source code is governed by a BSD-style  
// license that can be found in the LICENSE file.package main  
package main  
  
import "fmt"  
  
// Send the sequence 2, 3, 4, ... to channel 'ch'.  
func generate(ch chan int) {  
    for i := 2; ; i++ {  
       fmt.Printf("生成 %d\n", i)  
       ch <- i // Send 'i' to channel 'ch'.  
    }  
}  
  
// Copy the values from channel 'in' to channel 'out',  
// removing those divisible by 'prime'.  
func filter(in, out chan int, prime int) {  
    for {  
       i := <-in // Receive value of new variable 'i' from 'in'.  
       if i%prime != 0 {  
          out <- i // Send 'i' to channel 'out'.  
       } else {  
          fmt.Printf("❌ %d (被 %d 整除)\n", i, prime)  
       }  
    }  
}  
  
// The prime sieve: Daisy-chain filter processes together.
func main() {  
    ch := make(chan int) // Create a new channel.  
    go generate(ch)      // Start generate() as a goroutine.  
    for {  
       prime := <-ch  
       fmt.Println("✅", prime)  
       ch1 := make(chan int)  
       go filter(ch, ch1, prime)  
       ch = ch1  
       if prime > 50 {  
          break  
       }  
    }  
}
```

### 输出结果类似如下

```shell
生成 2
生成 3
✅ 2
✅ 3
生成 4
生成 5
❌ 4 (被 2 整除)
✅ 5
生成 6
生成 7
❌ 6 (被 2 整除)
✅ 7
生成 8
生成 9
❌ 8 (被 2 整除)
❌ 9 (被 3 整除)
生成 10
生成 11
❌ 10 (被 2 整除)
✅ 11
生成 12
生成 13
❌ 12 (被 2 整除)
✅ 13
生成 14
生成 15
❌ 14 (被 2 整除)
❌ 15 (被 3 整除)
生成 16
生成 17
❌ 16 (被 2 整除)
✅ 17
生成 18
生成 19
❌ 18 (被 2 整除)
✅ 19
生成 20
生成 21
❌ 20 (被 2 整除)
❌ 21 (被 3 整除)
生成 22
生成 23
❌ 22 (被 2 整除)
✅ 23
生成 24
生成 25
❌ 24 (被 2 整除)
❌ 25 (被 5 整除)
生成 26
生成 27
❌ 26 (被 2 整除)
❌ 27 (被 3 整除)
生成 28
生成 29
❌ 28 (被 2 整除)
生成 30
生成 31
❌ 30 (被 2 整除)
生成 32
❌ 32 (被 2 整除)
生成 33
生成 34
生成 35
❌ 33 (被 3 整除)
✅ 29
✅ 31
❌ 34 (被 2 整除)
❌ 35 (被 5 整除)
生成 36
生成 37
❌ 36 (被 2 整除)
生成 38
生成 39
❌ 38 (被 2 整除)
✅ 37
生成 40
生成 41
❌ 40 (被 2 整除)
生成 42
❌ 39 (被 3 整除)
✅ 41
❌ 42 (被 2 整除)
生成 43
生成 44
❌ 44 (被 2 整除)
生成 45
生成 46
❌ 45 (被 3 整除)
❌ 46 (被 2 整除)
生成 47
生成 48
❌ 48 (被 2 整除)
生成 49
生成 50
❌ 50 (被 2 整除)
✅ 43
❌ 49 (被 7 整除)
生成 51
✅ 47
生成 52
❌ 52 (被 2 整除)
❌ 51 (被 3 整除)
生成 53
生成 54
❌ 54 (被 2 整除)
生成 55
生成 56
❌ 56 (被 2 整除)
生成 57
生成 58
❌ 55 (被 5 整除)
❌ 57 (被 3 整除)
❌ 58 (被 2 整除)
生成 59
生成 60
✅ 53
❌ 60 (被 2 整除)
生成 61
生成 62
❌ 62 (被 2 整除)
生成 63
生成 64
❌ 63 (被 3 整除)
❌ 64 (被 2 整除)

进程 已完成，退出代码为 0
```

### 新单词

sieve /sɪv/
n. 筛子，滤网
v. 筛选

prime /praɪm/
prime number [数]质数,素数
prime time 黄金时段
prime minister /praɪm ˈmɪnɪstə/ 总理,首相
