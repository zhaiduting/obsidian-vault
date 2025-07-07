ADB 是 Android Debug Bridge 的缩写。首先使用 `adb devices` 查看已连接的设备，结果为空，说明手机没有连接。

```
~ > adb devices
List of devices attached

~ >
```

### 手机开启无线调试功能

进入手机的 `设置 » 系统管理与升级 » 开发者选项` 页面

1. 打开页面顶部的 `开发者选项` 开关
2. 下滑页面，找到 `无线调试` 开关，点击开启
3. 然后在 `无线调试` 这几个字上点击一下，这会进入 `无线调试` 页面

![无线调试](https://lib.zhaiduting.work.gd/uPic/%E6%97%A0%E7%BA%BF%E8%B0%83%E8%AF%95.jpg)

### 配对

无论是蓝牙耳机、蓝牙鼠标或键盘，首次使用时必须配对。与此类似，首次开启手机无线调试功能时也是需要进行配对的。

在手机的无线调试页面，点击 `使用配对码配对设备`。此时在电脑的终端敲入 `adb pair` 命令（跟上手机上显示的 IP 地址及临时端口号），最后敲入手机上显示的配对码进行配对。类似下面这样

```
~ > adb pair 192.168.50.217:39729
Enter pairing code: 342569
Successfully paired to 192.168.50.217:39729 [guid=adb-1558369949000TE-dPaZrc]
```

### 连接

配对成功并不代表已经连接，还需要敲入 `adb connect` 命令进行连接（跟上手机的 IP 及端口号）。

```
~ > adb connect 192.168.50.217:39607
connected to 192.168.50.217:39607
~ >
~ > adb devices
List of devices attached
192.168.50.217:39607	device
adb-1558369949000TE-dPaZrc._adb-tls-connect._tcp	device
```

注意：电脑重启后无需再次配对，但需要重新连接。
