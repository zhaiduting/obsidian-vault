本文介绍如何在虚拟机中安装路由器，[点击查看视频教程](https://www.youtube.com/watch?v=RW7MV4qlMUU&t=250s)
1、下载固件 https://downloads.openwrt.org/releases/24.10.0-rc7/targets/armsr/armv8/
文件为压缩包，解压后得到 img 格式的文件，在文件名结尾直接添加 .hdd 后缀（以此作虚拟机的硬盘）。
2、在 Parallels 中创建虚拟路由器

- 通过映像文件安装虚拟机时，勾选==没有指定源也继续==；
- 虚拟网卡选择桥接-默认适配器；
- 操作系统选择其它 Linux；
- 名称随意，勾选==安装前设定==；
- ==更改虚拟机硬盘为之前下载的文件== `openwrt-24.10.0-rc7-armsr-armv8-generic-ext4-combined-efi.img.hdd`，这会触发硬盘格式的自动转换
    ![](/assets/img自动转hdd.png)
    3、启动虚拟机后没有图形界面，只显示一个黑底白字的终端。敲入以下命令

```shell
vi /etc/config/network
# 编辑虚拟路由器的 IP 地址 192.168.50.2
# 按 esc 键，再敲入 :x 保存并退出
reboot
```

4、在宿主机中通过浏览器访问 192.168.50.2 可以打开虚拟路由器的 web 管理界面，也可以使用宿主机的终端敲入 ssh root@192.168.50.2 连接到虚拟路由器
