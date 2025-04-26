文件开头添加`//go:build debug`表示生产环境下不会编译此文件，例如以下仿写 PHP 的 var_dump 的文件便是一例。

```go
//go:build debug

package pkg

import (
    "fmt"
    "reflect"    "strings")

// VarDump 接收任意类型的变量并打印其详细信息
func VarDump(v interface{}) {
    fmt.Println("--- Var Dump ---")
    dumpValue(reflect.ValueOf(v), 0)
    fmt.Println("--- End Var Dump ---")
}
func dumpValue(v reflect.Value, level int) { ... }
```

GoLand 默认情况下也不会编译此文件，导致调用了 VarDump 函数的代码报错。

### 步骤 1

配置`GoLand / 设置 / Go / 构建标记 / 自定义标记`，加入`debug`后保存
![构建标记](https://lib.zhaiduting.work.gd/uPic/%E6%9E%84%E5%BB%BA%E6%A0%87%E8%AE%B0.png)

### 步骤 2

修改 air 的默认配置，首先生成一个 .air.toml 文件

```shell
goblog > air init

  __    _   ___
 / /\  | | | |_)
/_/--\ |_| |_| \_ v1.61.7, built with Go go1.24.1

.air.toml file created to the current directory with the default settings

```

修改文件中的 cmd 配置项，增加`-tags=debug`标记，如下所示

```toml
[build]
args_bin = []
bin = "./tmp/main"
#cmd = "go build -o ./tmp/main ."
cmd = "go build -tags=debug -o ./tmp/main ."
```

之后终端敲入 air 命令便会编译之前被忽略的文件了。
