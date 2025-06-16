主要作用是设置文件的默认打开方式，参考链接 https://github.com/moretension/duti/

> duti is a command-line utility capable of setting default applications for various document types on [macOS](https://www.apple.com/macos/), using Apple's [Uniform Type Identifiers](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html) (UTI). A UTI is a unique string describing the format of a file's content. For instance, a Microsoft Word document has a UTI of `com.microsoft.word.doc`. Using `duti`, the user can change which application acts as the default handler for a given UTI.

译文："duti" 是一个命令行工具，可以在 macOS 上设置各种文档类型的默认应用程序，使用的是苹果公司的统一类型标识符（UTI）。UTI 是一个独特的字符串，用于描述文件内容的格式。例如，微软 Word 文档的 UTI 是 com.microsoft.word.doc。通过使用 duti，用户可以更改哪个应用程序作为特定 UTI 的默认处理程序。

### 安装

```bash
brew install duti
```

### 用法

以下终端输出显示了 txt 文件及 ssml 文件的默认打开方式都是 TextEdit（文本编辑器）

```bash
~ > duti -x txt
TextEdit
/System/Applications/TextEdit.app
com.apple.TextEdit
~ >
~ > duti -x ssml
TextEdit
/System/Applications/TextEdit.app
com.apple.TextEdit
```
