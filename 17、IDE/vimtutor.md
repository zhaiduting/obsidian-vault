终端敲入命令 `vimtutor` 可以打开 vim 自带的交互式教程。

教程第一讲第一节：移动光标。类比桌面网游，使用 W、S、A、D 键可以控制人物向前、向后、向左、向右移动。而 vim 则使用 h、j、k、l 控制光标的移动方向。

问题是苹果系统与 Window 不同，按住一个键不放，会弹出重音符号菜单，而不是控制光标继续移动。可以通过以下命令解决这个问题

```sh
defaults write -g ApplePressAndHoldEnabled 0
```

如果不放心，可以敲入以下命令进行验证（结果为 0 表示修改成功）

```sh
~ > defaults read -g ApplePressAndHoldEnabled   
0
```

此时退出终端并重新打开，再次进入 vim 教程，按住 j 键不放，光标就可以不停的向下移动了。

### 网上资料

Vim 从入门到精通 https://wsdjeg.net/vim-galore-zh-cn/
