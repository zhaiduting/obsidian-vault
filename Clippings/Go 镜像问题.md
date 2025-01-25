---
source: https://chat.deepseek.com/a/chat/s/9e44bc34-3ad2-4d1e-be17-3640beff45df
author:
  - DeepSeek
created: 2025-01-26
description: Chat with DeepSeek AI – your intelligent assistant for coding, content creation, file reading, and more. Upload documents, engage in long-context conversations, and get expert help in AI, natural language processing, and beyond. | 深度求索（DeepSeek）助力编程代码开发、创意写作、文件处理等任务，支持文件上传及长文本对话，随时为您提供高效的AI支持。
tags:
  - clippings
---

的确是 ~/Library/Application Support/go/env 在起作用。我现在想要弄清楚的是：如果 ~/.zshrc 文件中配置了不同的 GOPROXY，会不会不生效？

```shell
~ > cat 'Library/Application Support/go/env'
GOPROXY=https://goproxy.cn,direct
```

---

从你的描述来看，`~/Library/Application Support/go/env` 文件中设置了 `GOPROXY=https://goproxy.cn,direct`，而你想知道如果在 `~/.zshrc` 中配置了不同的 `GOPROXY`，是否会生效。

---

### 1\. Go 配置的优先级

Go 的配置加载优先级如下（从高到低）：

1. **环境变量**：通过 `export GOPROXY=...` 设置的环境变量优先级最高。
2. **用户配置文件**：通过 `go env -w` 写入的配置文件（`~/Library/Application Support/go/env`）。
3. **Go 安装目录的默认配置文件**：如 `~/sdk/go1.23.4/go.env`。
4. **Go 的默认值**：如果以上都没有设置，Go 会使用默认值（如 `https://proxy.golang.org,direct`）。

因此，`~/.zshrc` 中设置的环境变量优先级高于 `~/Library/Application Support/go/env` 文件中的配置。

---

### 2\. 测试 `~/.zshrc` 的配置是否会生效

你可以在 `~/.zshrc` 中添加以下内容来测试：

```shell
export GOPROXY=https://goproxy.io,direct
```

然后运行以下命令使配置生效：

```shell
source ~/.zshrc
```

接着运行以下命令检查 `GOPROXY` 的值：

```shell
go env GOPROXY
```

如果输出为 `https://goproxy.io,direct`，说明 `~/.zshrc` 中的配置生效了。

---

### 3\. 验证优先级

为了验证优先级，你可以按照以下步骤操作：

1. 在 `~/.zshrc` 中添加：

```shell
export GOPROXY=https://goproxy.io,direct
```

2. 使配置生效：

```shell
source ~/.zshrc
```

3. 检查 `GOPROXY` 的值：

```shell
go env GOPROXY
```

如果输出为 `https://goproxy.io,direct`，说明 `~/.zshrc` 中的配置覆盖了 `~/Library/Application Support/go/env` 文件中的配置。

---

### 4\. 总结

- **`~/.zshrc` 中的配置会生效**：因为环境变量的优先级高于 `~/Library/Application Support/go/env` 文件中的配置。
- **验证方法**：

- 在 `~/.zshrc` 中添加 `export GOPROXY=https://goproxy.io,direct`。
- 运行 `source ~/.zshrc` 使配置生效。
- 运行 `go env GOPROXY` 检查输出。

如果输出为 `https://goproxy.io,direct`，说明 `~/.zshrc` 中的配置生效了。

---

### 5\. 注意事项

- 如果你希望永久修改 `GOPROXY`，建议使用 `go env -w` 命令，因为它会写入 `~/Library/Application Support/go/env` 文件，并且不会受到 Shell 配置文件的影响。

```shell
go env -w GOPROXY=https://goproxy.io,direct
```

如果还有其他问题，请随时告诉我！
