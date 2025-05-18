`unsafe` 包是 Go 语言中一个强大但同时也非常危险的工具，它允许你绕过 Go 语言的类型安全系统，直接操作内存。由于其破坏类型安全的特性，**在生产代码中应谨慎使用，并尽可能避免使用**。通常只有在性能关键或者需要与底层系统交互的场景下才会考虑它。

`unsafe` 包的核心在于提供了 `Pointer` 类型，它代表了可以指向任意类型变量的指针。`unsafe` 包还提供了一些函数，用于在 `Pointer` 和其他类型之间进行转换，以及获取类型的大小、字段的偏移量等信息。

下面我们来逐个查看 `unsafe` 包的基本用法：

### `unsafe.Pointer` 类型

`unsafe.Pointer` 类似于 C 语言中的 `void*`，它可以持有任何类型变量的内存地址。但是，`unsafe.Pointer` 本身不能进行指针运算，也不能直接解引用。你需要先将其转换为具体的类型指针才能进行操作。

### 类型之间的转换

`unsafe` 包允许你在不同类型的指针之间进行转换：

- **`*T` to `unsafe.Pointer`**: 任何类型的指针都可以安全地转换为 `unsafe.Pointer`。
- **`unsafe.Pointer` to `*T`**: `unsafe.Pointer` 可以转换回任何类型的指针 `*T`。**这是一个不安全的操作，你需要确保 `unsafe.Pointer` 指向的内存确实是 `T` 类型的。**
- **`uintptr` to `unsafe.Pointer`**: `uintptr` 是一个无符号整型，它足够大以容纳任何指针的整数值。你可以将 `uintptr` 转换为 `unsafe.Pointer`。**这个操作通常用于指针运算，但也需要非常小心，因为垃圾回收器不会追踪 `uintptr` 指向的内存。**
- **`unsafe.Pointer` to `uintptr`**: 你可以将 `unsafe.Pointer` 转换为 `uintptr` 以进行指针运算。

### `unsafe.Sizeof` 函数

`unsafe.Sizeof(x)` 函数返回变量 `x` 所占用的字节大小。这在需要与底层内存布局打交道或者进行序列化/反序列化时很有用。

```go
package main

import (
	"fmt"
	"unsafe"
)

type MyStruct struct {
	A int
	B bool
	C string // 注意：string 是一个描述符，实际数据在其他地方
}

func main() {
	var i int
	var b bool
	var s string
	var ms MyStruct

	fmt.Printf("Size of int: %d bytes\n", unsafe.Sizeof(i))
	fmt.Printf("Size of bool: %d bytes\n", unsafe.Sizeof(b))
	fmt.Printf("Size of string: %d bytes\n", unsafe.Sizeof(s))
	fmt.Printf("Size of MyStruct: %d bytes\n", unsafe.Sizeof(ms))
}

/* 64位机输出如下
Size of int: 8 bytes
Size of bool: 1 bytes
Size of string: 16 bytes
Size of MyStruct: 32 bytes
*/
```

### `unsafe.Offsetof` 函数

`unsafe.Offsetof(s.f)` 函数返回结构体 `s` 的字段 `f` 相对于结构体起始地址的偏移量（以字节为单位）。这对于直接操作结构体的内存布局非常有用。

```go
func main() {
	var ms MyStruct
	fmt.Printf("Offset of A: %d bytes\n", unsafe.Offsetof(ms.A))
	fmt.Printf("Offset of B: %d bytes\n", unsafe.Offsetof(ms.B))
	fmt.Printf("Offset of C: %d bytes\n", unsafe.Offsetof(ms.C))
}

/* 64位机输出如下
Offset of A: 0 bytes
Offset of B: 8 bytes
Offset of C: 16 bytes
*/
```

### `unsafe.Alignof` 函数

`unsafe.Alignof(x)` 函数返回变量 `x` 的对齐方式。对齐是指变量在内存中的起始地址必须是其大小的倍数，这有助于提高 CPU 的访问效率。

```go
package main

import (
	"fmt"
	"unsafe"
)

type MyStruct struct {
	A int8
	B int16
	C int32
}

func main() {
	var i8 int8
	var i16 int16
	var i32 int32
	var ms MyStruct

	fmt.Printf("Align of int8: %d bytes\n", unsafe.Alignof(i8))
	fmt.Printf("Align of int16: %d bytes\n", unsafe.Alignof(i16))
	fmt.Printf("Align of int32: %d bytes\n", unsafe.Alignof(i32))
	fmt.Printf("Align of MyStruct: %d bytes\n", unsafe.Alignof(ms))
}
/*
Align of int8: 1 bytes
Align of int16: 2 bytes
Align of int32: 4 bytes
Align of MyStruct: 4 bytes
*/
```

### 举个关于数组的例子

以下示例实际上相当于`fmt.Println(arr[2])`，只不过是改用了 unsafe 的写法

```go
package main

import (
	"fmt"
	"unsafe"
)

func main() {
	arr := [3]int{10, 20, 30}
	ptrToArr := &arr[0]
	offset := uint(2)
	sizeOfInt := unsafe.Sizeof(arr[0])

	// 使用 unsafe 包进行指针运算
	ptrToSecond := (*int)(unsafe.Pointer(uintptr(unsafe.Pointer(ptrToArr)) + uintptr(offset)*uintptr(sizeOfInt)))
	fmt.Printf("%v\n", *ptrToSecond) // 输出 30
}
```

### 多行改写的示例

上例计算 ptrToSecond 的代码只有一行，不便阅读。改写成多行形式如下

```go
ptrToSecond := (*int)(
    unsafe.Pointer(
       uintptr(unsafe.Pointer(ptrToArr)) +
       uintptr(offset)*uintptr(sizeOfInt),
    ),
)
```

这段代码展示了  **Go 中实现指针运算的标准方式**，通过：

1. `unsafe.Pointer`  擦除指针类型。
2. `uintptr`  转换为数值进行地址计算。
3. 最终转换回目标指针类型。

具体来说，采用 unsafe 读取 arr[2] 的值，关键是要拿到 arr[2] 的 Pointer，之后就可以通过强制类型转换得到 int 类型的指针了。

想要拿到 arr[2] 的 Pointer，就要涉及到指针运算。**指针运算的本质**：通过内存地址的数值计算（如加减偏移量），访问相邻或特定位置的数据。虽然 Go 本身  **不支持直接指针运算**（如 C 的  `ptr + offset`），但可以通过  `unsafe`  包实现类似功能。

在 Go 中，**只有  `uintptr`  类型的值才能显式地进行指针算术运算**。

```
arr[2] 的 uintptr
 = arr[0] 的 uintptr + 内存偏移量
 = arr[0] 的 uintptr + 2 的 uintptr * int 类型占用的字节数
```

考虑到 unsafe.Sizeof() 的返回值类型已经是 uintptr 了，因此 uintptr(sizeOfInt) 这步转换实际上是多余的。另外，无类型常量数字 2 在参与运算时，Go 编译器会根据上下文自动将其转换为匹配的类型（这里是  `uintptr`）。因此，之前的计算可以进一步简化成如下形式

```go
// 删除 offset := uint(2)
// ✅ 以下为安全且便于阅读的多行写法

ptrToSecond := (*int)(
    unsafe.Pointer(
       uintptr(unsafe.Pointer(ptrToArr)) + 2*sizeOfInt,
    ),
)
```

算出的 arr[2] 的 uintptr 值，实际上就是 arr[2] 在内存中的地址。要想访问此地址存储的数据，必须将 uintptr 转换成 Pointer 后再进一步转换成 int 指针。

### 避免  `uintptr`  临时变量导致 GC 问题

#### ⚠️ 以下为危险的多行写法

```go
// 使用 unsafe 包进行指针算术（多行版本）
ptr := unsafe.Pointer(ptrToArr)
addr := uintptr(ptr)
addr += 2 * sizeOfInt
ptrToSecond := (*int)(unsafe.Pointer(addr))
```

采用中间变量临时存储 uintptr 值是危险的， 因为`uintptr`  是一个普通的整数类型，**不持有对象的引用**，因此 Go 的垃圾回收器（GC）不会认为  `uintptr`  变量仍然在引用某个对象。

如果在  `uintptr`  计算期间，Go 的 GC 回收了原始对象，那么后续的  `unsafe.Pointer(addr)`  可能会指向无效内存，导致  **悬垂指针（dangling pointer）**，进而引发未定义行为（如程序崩溃、数据损坏等）。

#### ✅ 正确做法

最佳写法前文已述，要点：保持  `unsafe.Pointer`  直接转换，避免中间  `uintptr`  存储。或者改用单行表达式内完成计算也是可以的，但是单行写法不便于阅读。

再次整理最佳写法如下

```go
package main

import (
	"fmt"
	"unsafe"
)

func main() {
	arr := [3]int{10, 20, 30}
	ptrToArr := &arr // 地址等于 &arr[0]
	sizeOfInt := unsafe.Sizeof(arr[0])

	// 使用 unsafe 包进行指针运算
	ptrToSecond := (*int)(
		unsafe.Pointer(
			uintptr(unsafe.Pointer(ptrToArr)) + 2*sizeOfInt,
		),
	)
	fmt.Printf("%v\n", *ptrToSecond) // 输出 30
}
```

### 类型转换的问题

✅ 合法转换：`unsafe.Pointer(uintptr(p) + offset)`，**只在表达式中使用临时 uintptr，可以追踪原始指针**

❌ 不合法转换：`unsafe.Pointer(x)` 其中 `x` 是 `uintptr` 类型变量，**GC 无法追踪，编译器拒绝**

简单来讲，尝试将 uintptr 类型的变量转换为 Pointer 类型，是非法的。只有表达式中计算出的 uintptr 值才可以合法转换成 Pointer 类型。论证如下

```go
func main() {
	arr := [3]int{10, 20, 30}
	ptrToArr := &arr
	uintptrToArr2 := uintptr(unsafe.Pointer(ptrToArr)) + 2*8

	// 使用 unsafe 包进行指针运算
	ptrToSecond := (*int)(
		unsafe.Pointer(
			//uintptrToArr2, // 非法
			uintptr(unsafe.Pointer(ptrToArr)) + 16, // 合法
		),
	)

	fmt.Println(*ptrToSecond, uintptrToArr2)
}
```

简单起见，以上 ptrToSecond 的计算过程中，省略了中间变量 sizeOfInt，直接改用写死的 16 了（适用于 64 位机）。

如果尝试将表达式中 uintptr(unsafe.Pointer(ptrToArr)) + 16 的计算过程改用 uintptr 类型的中间变量 uintptrToArr2 进行替换，就会报错：possible misuse of unsafe.Pointer

```go
	uintptrToArr2 := uintptr(unsafe.Pointer(ptrToArr)) + 2*8

	ptrToSecond := (*int)(
		unsafe.Pointer(
			uintptrToArr2, // 非法
		),
	)
```

### 官方资料

文档链接 https://pkg.go.dev/unsafe
