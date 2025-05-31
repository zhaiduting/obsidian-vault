埃拉托斯特尼筛法，难懂得一逼！以下代码筛选了 50 以内的质数

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
          fmt.Printf("❌ %d %% %d = 0\n", i, prime)
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
❌ 4 % 2 = 0
✅ 5
生成 6
生成 7
❌ 6 % 2 = 0
生成 8
生成 9
✅ 7
❌ 8 % 2 = 0
❌ 9 % 3 = 0
生成 10
生成 11
❌ 10 % 2 = 0
生成 12
生成 13
❌ 12 % 2 = 0
✅ 11
✅ 13
生成 14
生成 15
❌ 14 % 2 = 0
❌ 15 % 3 = 0
生成 16
生成 17
❌ 16 % 2 = 0
✅ 17
生成 18
生成 19
❌ 18 % 2 = 0
✅ 19
生成 20
生成 21
❌ 20 % 2 = 0
❌ 21 % 3 = 0
生成 22
生成 23
❌ 22 % 2 = 0
生成 24
生成 25
❌ 24 % 2 = 0
✅ 23
生成 26
生成 27
❌ 26 % 2 = 0
❌ 27 % 3 = 0
❌ 25 % 5 = 0
生成 28
生成 29
❌ 28 % 2 = 0
✅ 29
生成 30
生成 31
❌ 30 % 2 = 0
✅ 31
生成 32
生成 33
❌ 32 % 2 = 0
❌ 33 % 3 = 0
生成 34
生成 35
❌ 34 % 2 = 0
❌ 35 % 5 = 0
生成 36
生成 37
❌ 36 % 2 = 0
✅ 37
生成 38
生成 39
❌ 38 % 2 = 0
❌ 39 % 3 = 0
生成 40
生成 41
❌ 40 % 2 = 0
生成 42
生成 43
✅ 41
❌ 42 % 2 = 0
生成 44
生成 45
✅ 43
❌ 44 % 2 = 0
❌ 45 % 3 = 0
生成 46
生成 47
❌ 46 % 2 = 0
生成 48
生成 49
❌ 48 % 2 = 0
✅ 47
❌ 49 % 7 = 0
生成 50
生成 51
❌ 50 % 2 = 0
❌ 51 % 3 = 0
生成 52
生成 53
❌ 52 % 2 = 0
✅ 53
生成 54
生成 55
❌ 54 % 2 = 0
❌ 55 % 5 = 0
生成 56
生成 57
❌ 56 % 2 = 0
❌ 57 % 3 = 0
生成 58
生成 59
❌ 58 % 2 = 0

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
