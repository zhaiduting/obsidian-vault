### 要实现的效果

屏幕左侧使用 RustRover 敲代码，右侧使用 Safari 实时预览。按下 ⌘ + tab 键切换到第 3 个 App 时，RustRover 和 Safari 一起被隐藏（相当于一个整体），再次按下 ⌘ + tab 键时，RustRover 和 Safari 一起显现。

> [!TIP]
> RustRover 默认没有 “Web Preview” 功能，无法实现左侧敲代码、右侧预览的效果。但是我们可以改用 macOS 的「台前调度」的「App 分组」功能实现目标，并且此功能更强大、更灵活、更具通用性。

### 具体步骤如下

1. Mac 开启「台前调度」
2. 打开 RustRover
3. 打开 Safari，此时 RustRover 自动缩回屏幕左侧
4. 将 RustRover 重新拖拽到屏幕中央，此时 Safari 和 RustRover 进入同一个分组
5. 在此分组内分别调整 Safari 和 RustRover 的位置及窗口大小，直到满意

### 参考文档

Mac 使用手册 » [使用台前调度整理 Mac 桌面](https://support.apple.com/zh-cn/guide/mac-help/mchl534ba392/26/mac/26)
