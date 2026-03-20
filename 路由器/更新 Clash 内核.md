### 查看旧内核版本

[使用 Termius 连接路由器](SSH%20连接.md)，之后敲入命令查看现有版本相关信息

```sh
zhaiduting@RT-BE88U-E830:/tmp/home/root# /koolshare/bin/clash -v
Mihomo Meta v1.19.13 linux arm64 with go1.25.0 Wed Aug 27 09:35:30 UTC 2025
Use tags: with_gvisor
```

这条信息不仅显示了“版本号”，还揭示了 **RT-BE88U** 路由器目前正处于**顶配运行状态**。我们可以把这条输出拆解开看：

| **字段**     | **你的当前值**    | **深度含义**                                                                                    |
| ------------ | ----------------- | ----------------------------------------------------------------------------------------------- |
| **内核分支** | `Mihomo Meta`     | 目前 Clash 社区最强分支，支持 Reality, Hysteria2 等最新协议。                                   |
| **版本号**   | `v1.19.13`        | 这是一个非常新的版本，完美适配 2025-2026 年的主流网络环境。                                     |
| **架构**     | **`linux arm64`** | **最关键点：** 这说明它正以 **64位原生模式** 运行，完全发挥了 BE88U 那颗 BCM4916 處理器的性能。 |
| **编译环境** | `go1.25.0`        | 使用了极高版本的 Go 编译器，对多核 CPU 调度和内存管理有显著优化。                               |
| **编译时间** | `2025年8月27日`   | 说明你的插件包不是过时的陈年旧货，而是专为新架构路由器准备的。                                  |
| **功能标签** | `with_gvisor`     | 支持高级网络栈监控，对处理 2.5G/10G 高速流量的稳定性非常有帮助。                                |

### 获取新内核

在 Mac 里打开电报 App 并访问 MCLite 频道，下载最新版 `mihomo-linux-arm64` 文件，将其保存到 `~/Downloads/` 文件夹下

### 上传新内核

无法通过 SFTP 将电脑里的文件直接上传到路由器，但是可以反过来操作，让路由器主动下载电脑里的文件。这需要在电脑里启用一个 Web 服务，命令如下

```sh
cd Downloads
python3 -m http.server 8000
```

用电脑验证一下服务是否可用，浏览器访问 `http://192.168.50.151:8000/` 如果能看到 `mihomo-linux-arm64` 则成功。这里的 `192.168.50.151` 需要替换成电脑当前使用的 IP 地址。

返回 Termius 窗口，敲入以下命令

```sh
# 进入内核所在的二进制目录
cd /koolshare/bin/

# 将旧内核改名为备份文件 (clash.bak)
mv clash clash.bak

# 下载电脑里的新内核并改名
wget http://192.168.50.151:8000/mihomo-linux-arm64 -O clash

# 关键一步：赋予新内核执行权限
chmod +x clash

# 验证版本，看看是不是你想要的新版
./clash -v
```

更新后的内核信息如下

```sh
zhaiduting@RT-BE88U-E830:/jffs/.koolshare/bin# ll clash
-rwxrwxrwx    1 zhaiduti root       7783864 Mar 20 23:58 clash*
zhaiduting@RT-BE88U-E830:/jffs/.koolshare/bin# ./clash -v
Mihomo Meta v1.19.21 linux arm64 with go1.26.1 Mon Mar  9 16:52:30 UTC 2026
Use tags: with_gvisor
```

**旧版本：** `v1.19.13` (2025年8月编译，基于 Go 1.25.0)。
**新版本：** **`v1.19.21`** (2026年3月9日编译，基于 **Go 1.26.1**)。

重新启动路由器后，手机、电脑仍然可以正常访问外网，这说明内核升级成功。由于路由器管理页面提示 JFFS 分区快满了，于是删除不再需要的 clash.bak 文件以释放空间。删除命令如下

```sh
rm clash.bak
```
