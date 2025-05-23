以下设置方法对于 PhpStorm 等等软件也有效，本文仅以 IDEA 为例说明。记住 `⌘ + 逗号`这个组合键，可以快速打开设置界面。
### 中文设置
打开设置界面，点击`外观与行为/系统设置/语言和地区`，点选`简体中文`，重启后生效。如果没有中文选项，点选`获取更多语言`可安装中文语言包。
![切换到中文界面](https://lib.zhaiduting.work.gd/uPic/%E5%88%87%E6%8D%A2%E5%88%B0%E4%B8%AD%E6%96%87%E7%95%8C%E9%9D%A2.png)
### 保存时的操作
在设置界面点击`工具/保存时的操作`， 勾选`重新设置代码格式`，之后修改代码，按下 ⌘ + s 发现文件保存时自动格式化功能生效，多余的空格可以被自动去掉。
![保存时的操作](https://lib.zhaiduting.work.gd/uPic/%E4%BF%9D%E5%AD%98%E6%97%B6%E7%9A%84%E6%93%8D%E4%BD%9C.png)
### 统一末尾分号
但是对于JS代码末尾的分号仍然没有统一，在设置界面点击`编辑器/代码样式/TypeScript`，点选`始终使用分号`，修改代码后保存，理应可以统一分号结尾。如果不想使用不必要的分号，只需点选`始终不使用分号`。另外，对于单、双引号的风格以及制表符与缩进等等，都可以在此界面统一设置。
![统一分号](https://lib.zhaiduting.work.gd/uPic/%E7%BB%9F%E4%B8%80%E5%88%86%E5%8F%B7.png)
### 设置未生效？
注意 .ts 文件和 .js 文件的区别，如果仅仅设置了 JS 结尾的分号，是不会影响 .ts 文件的。以下两项中的分号设置都应该统一修改
`编辑器/代码样式/JavaScript`
`编辑器/代码样式/TypeScript`
### 不要勾选未终止的语句
如果勾选了`编辑器/检查/JS和TS/代码样式问题/未终止的语句`，并且设置了`编辑器/代码样式`始终使用分号结尾，将会导致敲代码时可能出现一些烦人的提示（少了一个结尾的分号）。实际上，这个不必要的分号是不需要手动输入的，也不需为此提示。既然保存代码的时候，这些问题会被 IDE 自动解决，那人脑就完全没必要操心这些琐事了。

所以，下图中`未终止的语句`右侧的对勾应当去掉（ 默认情况下也是没有勾选这项的）。
![检查分号](https://lib.zhaiduting.work.gd/uPic/%E6%A3%80%E6%9F%A5%E5%88%86%E5%8F%B7.png)
### 代码风格设置小结
1. 勾选重新设置代码格式
2. 分别统一JS及TS的标点设置
3. 无需检查未终止的语句
