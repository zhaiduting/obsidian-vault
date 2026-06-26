在 Moonlight 中玩魔兽世界时，我惊奇的发现「Shift + 滚轮」不再有效。原因是 macOS 自作聪明的将「Shift + 滚轮」转变成了横向滚动事件，导致游戏接收不到正确的按键码。另外，苹果的「command + 滚轮」也失效了。

可以通过 LinearMouse 解决这两个修饰键的问题。

### 安装

```sh
brew install --cask linearmouse
```

### 解决「Shift + 滚轮」问题

![取消shift横向滚动](https://lib.zhaiduting.work.gd/uPic/%E5%8F%96%E6%B6%88shift%E6%A8%AA%E5%90%91%E6%BB%9A%E5%8A%A8.png)
滚动 » **横向** » 修饰键 » ⇧ (Shift) 设置为「忽略修饰键」  
返回 Moonlight 后，「Shift + 滚轮」立即生效！

### 解决「Cmd + 滚轮」问题

![替换cmd为缩放](https://lib.zhaiduting.work.gd/uPic/%E6%9B%BF%E6%8D%A2cmd%E4%B8%BA%E7%BC%A9%E6%94%BE.png)
滚动 » **纵向** » 修饰键 » ⌘ (Command) 设置为「缩放」  
返回 Moonlight 后，「Cmd + 滚轮」也会生效！

> [!WARNING]
> 「Cmd + 滚轮」事件实际发送的是数字小键盘的 + 号键的键位码！也就是说执行「Cmd + 滚轮下翻」实际效果相当于按下了小键盘的加号键；「Cmd + 滚轮上翻」相当于小键盘的减号键
