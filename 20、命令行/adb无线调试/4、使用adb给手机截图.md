使用 adb 命令截图，并将**截图直接保存到电脑桌面**，命令如下。注意，命令中必须使用 -p 选项将图片保存为 png 格式（省略 -p 将导致图片无效）。

```shell
adb shell screencap -p > $HOME/Desktop/Adbshot_$(date +%Y%m%d_%H%M%S).png
```

想要保存到手机相册？那就直接用手机截图啊！问题是有些截图在手机上无法完成，例如锁屏界面的截图、控制中心的截图，等等，手机上没法直接截图。于是以下命令就有了用武之地了，它可以截取手机上无法完成的截图，并保存到手机里。

```shell
# macOS 终端里的命令
adb shell screencap -p "/sdcard/DCIM/Screenshots/ADBShot_$(date +%Y%m%d_%H%M%S).png"

# 以下命令在 Git bash 中执行，兼容 Windows 的写法！
adb shell 'screencap -p "/sdcard/DCIM/Screenshots/ADBShot_$(date +%Y%m%d_%H%M%S).png"'
```

例如以下手机解锁时的动画截图，不使用 adb 命令，改用手机默认的截图方式似乎没办法完成。

![Adbshot_20250711_035843](https://lib.zhaiduting.work.gd/uPic/Adbshot_20250711_035843.png)
