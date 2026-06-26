在 Moonlight 中玩魔兽世界时，我惊奇的发现「Shift + 滚轮」不再有效。原因是 macOS 自作聪明的将「Shift + 滚轮」转变成了横向滚动事件，导致游戏接收不到正确的按键码。

可以通过 LinearMouse 解决这个问题

### 安装

```sh
brew install --cask linearmouse
```

### 设置

![取消shift横向滚动](https://lib.zhaiduting.work.gd/uPic/%E5%8F%96%E6%B6%88shift%E6%A8%AA%E5%90%91%E6%BB%9A%E5%8A%A8.png)
滚动 » 横向 » 修饰键 » ⇧ (shift) 设置为「忽略修饰键」  
返回 Moonlight 后，「Shift + 滚轮」立即生效！
