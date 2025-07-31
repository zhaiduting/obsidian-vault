### KDE Connect 是什么？

**KDE Connect** 是由 KDE 社区开发的一个跨平台工具，它允许多种设备之间进行无缝通信（如 Android 手机和电脑），实现：

- 剪贴板同步
- 文件传输
- 通知同步
- 远程控制（鼠标、媒体播放）
- 发送短信（仅限部分系统）
- 链接推送等

KDE Connect 的官方客户端原本是为 **Linux 桌面（如 KDE Plasma）** 设计的，但后来也扩展到其他平台，如 Android 和 macOS（通过移植或社区支持）。

### 下载链接

安卓版
https://play.google.com/store/apps/details?id=org.kde.kdeconnect_tp

Mac 版
https://cdn.kde.org/ci-builds/network/kdeconnect-kde/master/macos-arm64/

由于苹果 App Store 下载的有问题，无法正常使用，而 kde.org 网站下载的好像也有点问题。因此不再使用 Mac 版的 KDE Connect 了，改用 soduto 替代。安装 soduto 的命令如下

```shell
brew install --cask soduto
```

### Soduto 是什么？

**Soduto** 是一款专为 **macOS** 开发的第三方 KDE Connect 兼容客户端，由个人开发者 Jonathan Hudson 创建。它遵循 KDE Connect 协议，因此能与 Android 端的 KDE Connect 完美通信。

> ⚠️ 注意：Soduto 并非 KDE 官方开发，而是一个 **第三方项目**，但它是早期 macOS 上 KDE Connect 功能的主要解决方案之一。

### 总结

实测发现，Apple M2 Pro 芯片的笔记本安装 soduto 后，与装有 KDE Connect 的 vivo X70 Pro+可以很好地通信。
