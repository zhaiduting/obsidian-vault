---
title: DeepSeek
source: https://chat.deepseek.com/a/chat/s/69b5484c-b610-4186-bdee-564636c5a283
created: 2025-04-02
tags:
  - clippings
  - 命令行
  - DeepSeek
---

在 `curl` 命令中， `-X` 选项用于指定 HTTP 请求方法（HTTP request method）。

在你给出的例子中：

```shell
$ curl -X POST http://localhost:3000/articles
```

`-X POST` 表示使用 **POST** 方法发送 HTTP 请求，通常用于向服务器提交数据（如创建新文章）。

### 其他常见的 HTTP 方法：

- `GET` （默认方法，用于获取数据）
- `POST` （提交数据，如创建资源）
- `PUT` （更新整个资源）
- `PATCH` （部分更新资源）
- `DELETE` （删除资源）

### 如果没有 -X 会怎样？

如果省略 `-X` ， `curl` 默认使用 `GET` 方法。例如：

```
$ curl http://localhost:3000/articles
```

等同于：

```shell
$ curl -X GET http://localhost:3000/articles
```

### 为什么需要 -X？

某些 API 要求特定的 HTTP 方法（如 `POST` 、 `PUT` 、 `DELETE` ），这时就需要用 `-X` 来明确指定。

### 补充说明：

- 如果 API 需要提交数据（如 JSON 或表单数据），通常还需要配合 `-d` （数据）和 `-H` （请求头）选项，例如：
    sh 复制
    ```shell
    curl -X POST http://localhost:3000/articles \
         -H "Content-Type: application/json" \
         -d '{"title":"New Article","content":"Hello World"}'
    ```

所以， `-X POST` 的作用就是告诉 `curl` 使用 **POST** 方法发送请求，而不是默认的 `GET` 。

> \-d 是 data 的意思，-H 是 head 的意思，那 -X 是哪个词的首写字母？

在 `curl` 命令中， `-X` 选项的 **X** 并不是某个英文单词的首字母缩写，而是代表 **"HTTP request method"（HTTP 请求方法）** 的一个通用符号。

### 为什么用 -X 而不是 -M（Method）？

- 在早期的 `curl` 版本中，选项通常采用 **短横线 + 单个字母** 的形式（如 `-d` 、 `-H` 、 `-X` ），而不是完整的单词。
- `X` 在计算机领域常用于表示 **"自定义" 或 "扩展"** （eXtension），比如：
    - `X-File` （扩展文件）
    - `X-Forwarded-For` （HTTP 扩展头部）
    - `X11` （X Window 系统）
- 因此， `-X` 可能代表 **"eXplicitly specify the HTTP method"（显式指定 HTTP 方法）** ，但官方文档并未明确解释其具体含义，只是约定俗成。

### 对比其他工具：

- 类似功能的工具（如 `httpie` ）使用更直观的选项，比如 `--method=POST` ，但 `curl` 保持了传统的短选项风格。

### 结论：

`-X` 是 `curl` 设计中的一个 **历史惯例** ，并非某个特定单词的缩写，但可以理解为 **"eXplicit method"（显式指定方法）** 或 **"HTTP request method"（HTTP 方法）** 的简写。
