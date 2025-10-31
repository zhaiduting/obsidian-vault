---
source: https://gemini.google.com/app/8ad57bb82f39e568
author:
  - Gemini
created: 2025-10-31
tags:
  - clippings
---

> git status 命令显示未跟踪的文件名称时经过了编码，我希望显示默认的中文字体，咋整？

![git中文乱码](https://lib.zhaiduting.work.gd/uPic/git%E4%B8%AD%E6%96%87%E4%B9%B1%E7%A0%81.png)

🎉 **解决 `git status` 中文文件名乱码问题**

你遇到的这种显示，如 `"Mac/\347\273\237\344\270\200\350\256\277\350\276\276\347\252\227\345\217\243\347\232\204\350\256\276\347\275\256.md"` ，是 Git 默认的路径引用（Path Quoting）行为。它会对非 ASCII 字符（值大于 `0x80` 的字节）进行八进制转义编码（quoted octal notation），例如你看到的 `\347\273\237` 就是中文 UTF-8 编码的一部分。

要让 `git status` 直接显示中文文件名，你需要关闭这个路径引用功能。

### 解决方法

在你的终端中运行以下命令：

```sh
git config --global core.quotepath false
```

- **`git config`**: Git 的配置命令。
- **`--global`**: 将配置应用到当前用户的所有 Git 仓库（写入 `~/.gitconfig` 文件）。如果你只想对当前仓库生效，可以省略 `--global` 。
- **`core.quotepath false`**: 将 `core.quotepath` 设置为 `false` ，这样 Git 就不会对路径中的非 ASCII 字符进行转义编码了。

### 操作步骤

1. **打开终端或 Git Bash** 。
2. **输入命令并执行**:

```sh
   git config --global core.quotepath false
```

3. **再次运行 `git status`** ： 现在，你应该能看到正常的中文文件名了。

### 额外提示 (针对 Mac 用户)

如果你使用的是 macOS，并且在设置 `core.quotepath false` 后仍然遇到中文文件名问题（例如，文件被识别为未跟踪/已修改，而实际上文件名看起来是相同的，这是由于 macOS 默认的 Unicode 规范化形式 NFD 与 Git 常用的 NFC 差异导致的），你可能还需要进行以下设置：

```sh
git config --global core.precomposeunicode true
```

不过，对于你提供的这种 **乱码显示** 问题，通常只需要第一步： `git config --global core.quotepath false` 即可解决。

如果还有其他关于 Git 编码或配置的问题，随时可以问我！
