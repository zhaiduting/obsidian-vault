---
title: Go multi-module workspace简介
source: https://chatgpt.com/share/68166706-12ec-800e-90d2-e484cb2277e6
author:
  - "[[ChatGPT]]"
created: 2025-05-04
description: Shared via ChatGPT
tags:
  - clippings
---

Go 语言的 multi-module workspaces 有什么用？

###### ChatGPT 说：

Go 语言的 multi-module workspaces（多模块工作区）是从 Go 1.18 引入的一个特性，目的是为了**更方便地在多个 Go 模块之间协作开发**，而不需要频繁地使用 `replace` 指令。

---

### 一、以前的做法（没用 workspace）

假设你在开发两个模块：

- `github.com/user/lib`：一个公共库模块
- `github.com/user/app`：一个使用 `lib` 的应用模块

默认情况下，`app` 模块引用 `lib` 的最新版本需要通过 `go get` 拉取某个 tag 或 commit。如果你要调试或开发 `lib`，就需要在 `app/go.mod` 中写：

`replace github.com/user/lib => ../lib`

这种 `replace` 写法：

- 不方便多人协作（本地路径不一样）
- 不容易维护（多个模块都要写）
- 容易忘记清理

---

### 二、workspace 的做法（Go 1.18+）

你可以在项目根目录创建一个 `go.work` 文件，例如：

```bash
go work init ./lib ./app
```

生成的 `go.work` 会包含如下内容：

`go 1.18 use ( ./lib ./app )`

这样，Go 工具链就会自动在这两个模块之间建立连接：

✅ `app` 直接使用本地 `lib` 源码  
✅ 无需修改任何 `go.mod`  
✅ 多人协作可以共享 `go.work` 文件或用 `.gitignore` 屏蔽它

---

### 三、总结：multi-module workspace 的作用

| 作用              | 说明                                               |
| ----------------- | -------------------------------------------------- |
| 🧩 本地模块开发   | 多个模块之间可以共享本地源码，而不用频繁 `replace` |
| 🔁 提高迭代效率   | 修改库模块无需发布 tag，应用模块即可实时引用       |
| 🤝 团队协作更清晰 | `go.work` 比 `replace` 更适合多人维护              |
| 📁 项目结构更整洁 | 不污染 `go.mod`，也更容易构建 monorepo 项目        |

---

需要我举个实际的文件结构和例子代码来演示它的用法吗？

##### 您说：

要

###### ChatGPT 说：

好的，我们通过一个实际例子来演示 Go 的 multi-module workspace 如何工作。

---

## 🧪 示例结构

我们创建一个包含两个模块的项目结构：

```
myproject/
├── go.work          <- 工作区定义文件
├── lib/             <- 库模块
│   ├── go.mod
│   └── lib.go
└── app/             <- 应用模块
    ├── go.mod
    └── main.go
```

---

## 🔧 第一步：创建库模块 `lib`

```bash
mkdir -p myproject/lib
cd myproject/lib
go mod init example.com/lib
```

**lib/lib.go**

```go
package lib

func Greet(name string) string {
    return "Hello, " + name
}
```

---

## 🧑‍💻 第二步：创建应用模块 `app`

```bash
mkdir -p ../app
cd ../app
go mod init example.com/app
```

**app/main.go**

```go
package main

import (
    "fmt"
    "example.com/lib" // 注意：直接引用 lib 模块
)

func main() {
    fmt.Println(lib.Greet("World"))
}
```

---

## 🧙‍♂️ 第三步：在项目根目录创建 `go.work`

```bash
cd ../
go work init ./lib ./app
```

生成的 `go.work` 会像这样：

```go
go 1.18

use (
    ./lib
    ./app
)
```

---

## 🏃‍♂️ 第四步：运行应用

```bash
cd app
go run main.go
```

输出应为：`Hello, World`

这说明虽然 `example.com/lib` 是一个独立模块，但通过 `go.work`，它被识别为当前 workspace 的一部分，**Go 自动将其替换为本地路径，无需写 replace。**

---

## 💡 总结重点

- `go.work` 像一个全局的 `replace` 管理器，但**不污染每个模块的 `go.mod` 文件**。
- 非常适合 monorepo 或多模块开发。
- 用完后如果要发布模块，删除 `go.work` 即可，不影响模块的正常发布。

是否需要我打包一个完整的示例压缩包或生成一个 Git 仓库结构的压缩文件供你试用？

##### 您说：

monorepo 是什么意思？

###### ChatGPT 说：

`monorepo` 是 **“mono repository”** 的缩写，意思是“单一代码仓库”。

---

## ✅ Monorepo 的定义：

> **Monorepo** 是指把多个项目（通常是多个模块、服务、库等）**统一放在同一个 Git 仓库里**进行版本控制和协作开发。

相对的另一种方式是 **multi-repo**，也就是每个模块/项目单独放一个仓库。

---

## 🧱 举个例子

### 👎 Multi-repo（多仓库）

```
- github.com/myorg/lib1       <- 一个库
- github.com/myorg/service-a  <- 一个微服务
- github.com/myorg/web-ui     <- 一个前端项目
```

每个项目都是一个独立仓库，管理起来分散。

---

### 👍 Monorepo（单仓库）

```
- github.com/myorg/monorepo/
  ├── lib1/
  ├── lib2/
  ├── service-a/
  └── web-ui/
```

所有项目都放在一个仓库里，统一管理、统一版本。

---

## 💡 Monorepo 的优点

| 优点              | 说明                                       |
| ----------------- | ------------------------------------------ |
| 🔁 模块间协作方便 | 不用跨仓库引用、clone，多模块之间直接调用  |
| 🔨 一致构建流程   | CI/CD、Lint、测试等统一配置                |
| 📦 更好依赖管理   | 共享依赖、共享工具链（如 Go 的 `go.work`） |
| 🔍 历史追踪清晰   | 所有变更都在一个地方看得见                 |

---

## 🧩 Go 的 Monorepo 举例

如果你有多个 Go 模块，比如：

```
- github.com/myorg/monorepo/
  ├── lib1/
  ├── lib2/
  ├── service-a/
  └── web-ui/
```

你就可以通过 `go.work` 把这些模块统一管理，开发起来就像一个整体项目一样高效。

## 如果改用 replace

删除`go.work`文件，将会导致前文的 app 项目无法运行。如果改用传统的 replace 做法，app 项目中的 go.mod 文件应该类似如下

```go
module example.com/app

go 1.24.2

require example.com/lib v0.0.0 // v0.0.0 代表“任意版本”

replace example.com/lib => ../lib // 符号 => 的左右两侧至少保留1个空格！
```

此时 app 应该可以正常运行了
