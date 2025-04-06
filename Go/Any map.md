以下 Obj 的键类型任意，值类型也任意，是一个 any 类型的 map

```go
package main

import "fmt"

type Obj map[interface{}]interface{}

func main() {
	m := Obj{"s": 3}
	m[2] = 2
	m['a'] = Animal{name: "Jack"}
}
```

虽然给`m['a']`明确赋值 Animal 类型，但是并不能直接使用 animal.name

```go
// m['a'].name undefined (type interface{} has no field or method name)
	fmt.Println(animal.name)
```

需要进行类型断言来告诉编译器 `m['a']` 其实是 `Animal` 类型。正确的写法如下：

```go
	// 类型断言
	animal, ok := m['a'].(Animal)
	if !ok {
		fmt.Println("类型断言失败")
		return
	}

	fmt.Println(animal.name)
```

如果确信 `m['a']` 存储的值一定是 `Animal` 类型，并且不想处理 `ok` 变量，你可以直接使用类型断言（不带 `ok`）：

```go
	// 直接类型断言，不做错误检查
	animal := m['a'].(Animal)
	fmt.Println(animal.name)
```
