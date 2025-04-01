---
author:
  - "[[Clippings/curl 命令]]"
source: https://chat.deepseek.com/a/chat/s/89bf7713-dd0d-4ada-943b-bd52f89d7243
published: 
created: 2025-01-19
tags:
  - clippings
  - 命令行
  - Go
---

在 Go 语言中，`%+v` 是 `fmt` 包中用于格式化输出的一个动词（verb），用于打印结构体时显示字段名和对应的值。

具体来说：

- `%v` 会打印结构体的值，但不显示字段名。
- `%+v` 会打印结构体的值，并且显示每个字段的字段名。

例如：

```go
package main

import (
	"fmt"
)

type Person struct {
	Name string
	Age  int
}

func main() {
	p := Person{Name: "Alice", Age: 30}
	fmt.Printf("%v\n", p)  // 输出: {Alice 30}
	fmt.Printf("%+v\n", p) // 输出: {Name:Alice Age:30}
}
```

在这个例子中：

- `%v` 只打印了结构体的值 `{Alice 30}`。
- `%+v` 打印了结构体的字段名和值 `{Name:Alice Age:30}`。

因此，`%+v` 在调试或需要查看结构体字段名时非常有用。
