引用[文档](https://golang.google.cn/doc/go1.4#internalpackages)的一段话

> Go’s package system makes it easy to structure programs into components with clean boundaries, but there are only two forms of access: local (unexported) and global (exported). Sometimes one wishes to have components that are not exported, for instance to avoid acquiring clients of interfaces to code that is part of a public repository but not intended for use outside the program to which it belongs.

Go 的包系统使得将程序结构化为具有清晰边界的组件变容易了，但它仅提供两种访问方式：本地（未导出）和全局（导出）。有时，人们希望拥有不被导出的组件，例如，防止获取代码接口的客户端，虽然这些代码是公共存储库的一部分，但并不打算在所属程序之外使用。

关于 internal packages 的详细内容可以参考[这篇文档](https://juejin.cn/post/6950094090969022472)
