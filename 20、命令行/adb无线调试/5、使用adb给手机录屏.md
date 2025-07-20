没办法直接将录屏文件保存到电脑桌面，只能分步走。首先录屏并保存到手机里

```shell
adb_recording=Adbrecording_$(date +%Y%m%d_%H%M%S).mp4
adb shell screenrecord /sdcard/DCIM/Screenshots/$adb_recording
```

之后在手机里演示操作步骤；然后在电脑终端按下 Ctrl + C 结束录屏；最后敲入以下命令下载刚刚录制的文件到桌面

```shell
adb pull /sdcard/DCIM/Screenshots/$adb_recording $HOME/Desktop/
```
