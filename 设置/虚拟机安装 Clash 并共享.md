### 目标

在 Win11 虚拟机中安装 Clash 并将其提供给宿主机、手机及 iPad 等设备使用。注意：不需要在所有的设备中分别各装一遍 Clash

### 下载并安装

这是一个访问外网的工具，下载链接为 [clashforwindows.net](https://www.clashforwindows.net/clash-for-windows-download/)，Mac 版下载链接为 [clashx.org](https://clashx.org/clashx-download/)

使用方法也简单，只需要把有效的==订阅链接==写到 Profiles 的 Download from a URL 文本框里，之后点击 Download 按钮就可以访问外网了
![[粘贴订阅地址.jpg]]

### 共享设置

首先设置虚拟机使用桥接网络，勾选默认适配器或者 WiFi 都可以。不要使用共享网络，因为它会导致虚拟机的 IP 地址与其它设备处于不同的网段。
![[虚拟机的网络设置.png]]
开启 Clash 的 Allow LAN 功能，这个就是用来给其他设备共享 Clash 的。记下 Port 号 7890，然后点击 network interfaces 按钮
![[Allow LAN.jpg]]

记下网络接口界面显示的虚拟机 IP 地址
![[查看虚拟机IP地址.jpg]]

### 手机的网络设置

在手机的 WiFi 设置界面，手动添加一个==代理服务器==，将以上端口号及虚拟机 IP 地址填进去。稍候，手机也可以访问外网了。注意：手机的 IP4 地址的前三段与代理主机的前三段必须相同（如下图中的两个 IP 都具有相同的 192.168.50 这三段数字）。
![[设置 wifi 代理.jpg]]

对于宿主机以及 iPad 等设备的网络设置方法与手机类似（以下为 Mac 宿主机截图）![[Mac 宿主机的代理设置.png]]
### 注意

- 所有设备都应该处于同一网段（IP 地址的前三个部分相同）
- 虚拟机不能关机，否则其他设备就无法访问外网。
