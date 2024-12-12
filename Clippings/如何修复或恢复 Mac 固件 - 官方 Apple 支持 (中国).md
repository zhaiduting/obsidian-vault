---
title: "如何修复或恢复 Mac 固件 - 官方 Apple 支持 (中国)"
source: "https://support.apple.com/zh-cn/108900"
author:
  - "[[Apple Support]]"
published:
created: 2024-12-13
description: "在极少数情况下，搭载 Apple 芯片或 Apple T2 安全芯片的 Mac 可能会停止响应，需要使用另一台 Mac 进行修复或恢复。"
tags:
  - "clippings"
---
[什么情况下需要修复或恢复](https://support.apple.com/zh-cn/#restore)

[修复或恢复时需要什么](https://support.apple.com/zh-cn/#requirements)

[如何准备笔记本电脑以进行修复或恢复](https://support.apple.com/zh-cn/#preparelaptop)

[如何准备台式电脑以进行修复或恢复](https://support.apple.com/zh-cn/#preparedesktop)

[如何修复或恢复](https://support.apple.com/zh-cn/#reviveorrestore)

如果储存在内存中的固件需要修复或恢复，则搭载 Apple 芯片或 Apple T2 安全芯片的 Mac 可能会停止响应。在某些极少数情况下可能会出现这个问题，例如当断电导致 macOS 安装中断时。症状可能包括：

- 在启动时显示一个[由圆圈围绕的感叹号](https://support.apple.com/zh-cn/102164)
- 显示固件恢复模式对应的[状态指示灯模式](https://support.apple.com/zh-cn/102768)
- 开机正常，但在启动时显示空白屏幕（对于[空白屏幕](https://support.apple.com/zh-cn/102575)问题，还存在其他原因和解决方案）

要在不抹掉任何文件的情况下解决这个问题，请修复 Mac 的固件。或者在修复不成功时进行恢复。

1. 受影响的 Mac，即需要修复或恢复的[搭载 Apple 芯片的 Mac](https://support.apple.com/zh-cn/116943) 或[搭载 Apple T2 安全芯片的 Mac](https://support.apple.com/zh-cn/103265)。其他 Mac 机型不适用。
2. 另一台 Mac，装有 [macOS Monterey 12.4 或更高版本](https://support.apple.com/zh-cn/109033)。你将使用这台 Mac 来修复或恢复受影响的 Mac。

- 如果这台 Mac 运行的是 macOS Monterey 或 macOS Ventura，请从 App Store [下载 Apple Configurator](https://apps.apple.com/cn/app/apple-configurator/id1037126344) 并打开这个 App，然后再继续处理。[无法下载？](https://support.apple.com/zh-cn/#download)
- 如果这台 Mac 运行的是 macOS Sonoma 或更高版本，则无需使用 Apple Configurator。
3. 同时支持数据传输和充电的 USB-C 至 USB-C![USB-C 至 USB-C](https://cdsassets.apple.com/live/7WUAS350/images/inline-icons/usb-icon.png) 连接线，例如部分 Apple 产品随附的 Apple USB-C 充电线。这款充电线可与 Mac 上接受以下 USB-C 类接头的[端口](https://support.apple.com/zh-cn/109523)搭配使用：雷雳 4、雷雳/USB 4、雷雳 3 或 USB 3。*请勿使用**雷雳 3*![雷雳 3](https://cdsassets.apple.com/live/7WUAS350/images/inline-icons/thunderbolt-icon.png) *充电线。*

你将使用 USB-C 连接线来连接两台电脑，然后在受影响的电脑上进入 DFU（设备固件更新）模式。

1. 确保两台电脑都已接入电源。
2. 在受影响的 MacBook Pro 或 MacBook Air 上，[识别 DFU 端口](https://support.apple.com/zh-cn/120694)，然后将 USB-C 连接线插入这个端口。
3. 在另一台 Mac 上：

- 将 USB-C 连接线的另一端插入任一 USB-C 端口。
- 确保这台 Mac 已接入互联网\*。
- 如果这台 Mac 运行的是 macOS Ventura 或 macOS Monterey，请打开 Apple Configurator。
4. 在受影响的 Mac 上，进入 DFU 模式：

1. 按住电源按钮最多 10 秒钟，直到 Mac 关机。（如果 Mac 配有触控 ID 按钮，这个按钮也是电源按钮。）如果 Mac 反而开机了，请重复这个步骤。
2. 按下并松开电源按钮，然后*立即*同时按住内建键盘上的以下四个按键：

- 键盘左侧的 Control ⌃ 键
- 键盘左侧的 Option ⌥ 键
- 键盘右侧的 Shift ⇧ 键
- 电源按钮

![MacBook Pro 键盘，显示了要按的四个按键](https://cdsassets.apple.com/live/7WUAS350/images/mac-os/sonoma/macbook-pro-keyboard-dfu-mode-startup-keyboard-combination-diagram.png)
3. 搭载 Apple 芯片的 Mac 笔记本电脑：

- 按住所有这四个按键约 10 秒钟，然后松开除电源按钮以外的所有按键。
- 继续按住电源按钮最多 10 秒钟。当另一台 Mac [在“访达”或 Apple Configurator 中显示 DFU 窗口](https://support.apple.com/zh-cn/#reviveorrestore)时，请松开电源按钮。如果那台 Mac 先显示一条提醒，询问你是不是允许配件进行连接，请松开电源按钮并点按“允许”。受影响的 Mac 现在会进入 DFU 模式，并且应该会显示空白屏幕。
4. 搭载 T2 芯片的 Mac 笔记本电脑：按住所有这四个按键约 3 秒钟。当另一台 Mac [在“访达”或 Apple Configurator 中显示 DFU 窗口](https://support.apple.com/zh-cn/#reviveorrestore)时，请松开所有按键。如果那台 Mac 先显示一条提醒，询问你是不是允许配件进行连接，请松开所有按键并点按“允许”。受影响的 Mac 现在会进入 DFU 模式，并且应该会显示空白屏幕。
5. 请按照下面的步骤使用“访达”或 Apple Configurator 进行修复或恢复。

你将使用 USB-C 连接线来连接两台电脑，然后在受影响的电脑上进入 DFU（设备固件更新）模式。

1. 在受影响的 Mac 上，[识别 DFU 端口](https://support.apple.com/zh-cn/120694)，然后将 USB-C 连接线插入这个端口。
2. 在另一台 Mac 上：

- 将 USB-C 连接线的另一端插入任一 USB-C 端口。
- 确保这台 Mac 已接入电源和互联网\*。
- 如果这台 Mac 运行的是 macOS Ventura 或 macOS Monterey，请打开 Apple Configurator。
3. 在受影响的 Mac 上，进入 DFU 模式：

1. 拔下 Mac 的电源线。
2. 按住电源按钮。
3. 继续按住电源按钮，同时将 Mac 接入电源，然后继续按住电源按钮最多 10 秒钟。当另一台 Mac [在“访达”或 Apple Configurator 中显示 DFU 窗口](https://support.apple.com/zh-cn/#reviveorrestore)时，请松开电源按钮。如果那台 Mac 先显示一条提醒，询问你是不是允许配件进行连接，请松开电源按钮并点按“允许”。受影响的 Mac 现在会进入 DFU 模式，并且应该会显示空白屏幕。
4. 请按照下面的步骤使用“访达”或 Apple Configurator 进行修复或恢复。

在按照上文所述[准备笔记本电脑](https://support.apple.com/zh-cn/#preparelaptop)或[准备台式电脑](https://support.apple.com/zh-cn/#preparedesktop)后，请使用“访达”或 Apple Configurator 来修复或恢复受影响的 Mac，具体使用哪个工具取决于另一台 Mac 上使用的 macOS 版本。

*Mac 已准备好从“访达”进行修复或恢复（macOS Sonoma 或更高版本）：*

![“访达”窗口中显示已在边栏中选择“Mac”](https://cdsassets.apple.com/live/7WUAS350/images/mac-os/sonoma/locale/zh-cn/macos-sonoma-finder-mac-dfu-mode-revive-restore-firmware.png)

*Mac 已准备好从 Apple Configurator 进行修复或恢复（macOS Monterey 12.4 或更高版本）：*

![Apple Configurator 窗口中显示已为受影响的 Mac 选择“DFU”](https://cdsassets.apple.com/live/7WUAS350/images/macos/sonoma/locale/zh-cn/apple-configurator-2-17-device-list-all-devices-mac-in-dfu-mode-selected.png)

先尝试修复。修复可能比恢复要快，而且不会抹掉 Mac。

1. 如果使用的是“访达”（需要 macOS Sonoma 或更高版本）：

1. 在“访达”窗口的边栏中，选择“Mac”，如上图所示。[没看到它？](https://support.apple.com/zh-cn/#help)
2. 点按“修复 Mac”按钮，然后点按“继续”进行确认。这个窗口中的进度条会显示正在进行修复。
2. 如果使用的是 Apple Configurator（需要 macOS Monterey 12.4 或更高版本）：

1. 打开 Apple Configurator，这个 App 可以从 App Store 下载。[无法下载？](https://support.apple.com/zh-cn/#download)
2. 在 Apple Configurator 窗口中，为受影响的 Mac 选择“DFU”，如上图所示。[没看到它？](https://support.apple.com/zh-cn/#help)
3. 从菜单栏中，选取“操作”>“高级”>“修复设备”。这个窗口中的进度条会显示正在进行修复。
3. 修复完成后，受影响的 Mac 会自动重新启动。如果它关机，请按下电源按钮将它开机。
4. 如果系统提示你选择要恢复的宗卷，请选择所需的宗卷（例如 Macintosh HD），然后点按“下一步”。
5. 如果系统提示你选择一个你知道密码的用户，请按提示操作，然后输入这个用户的登录密码。点按“下一步”，然后点按“重新启动”。
6. 搭载 Apple 芯片的 Mac：修复后的 Mac 会载入启动选项，其中包括[“选项”齿轮图标](https://support.apple.com/zh-cn/102342)。选择你的启动磁盘（例如 Macintosh HD），然后点按它下方显示的“继续”按钮。
7. 修复的 Mac 会完成启动，随后这个过程便告完成。

如果 Mac 无法修复，请按照以下步骤来抹掉它并将它恢复为出厂设置。

1. 如果使用的是“访达”（需要 macOS Sonoma 或更高版本）：

1. 在“访达”窗口的边栏中，选择“Mac”，如上图所示。[没看到它？](https://support.apple.com/zh-cn/#help)
2. 点按“恢复 Mac”按钮，然后点按“恢复并更新”进行确认。这个窗口中的进度条会显示正在进行恢复。
2. 如果使用的是 Apple Configurator（需要 macOS Monterey 12.4 或更高版本）：

1. 打开 Apple Configurator，这个 App 可以从 App Store 下载。[无法下载？](https://support.apple.com/zh-cn/#download)
2. 在 Apple Configurator 窗口中，为受影响的 Mac 选择“DFU”，如上图所示。[没看到它？](https://support.apple.com/zh-cn/#help)
3. 从菜单栏中，选取“操作”>“恢复”，然后点按“恢复”进行确认。这个窗口中的进度条会显示正在进行恢复。
3. 恢复完成后，受影响的 Mac 会自动重新启动。如果它关机，请按下电源按钮将它开机。
4. 如果系统提示你选择 Wi-Fi 网络或连接网线，请按提示操作。
5. 搭载 Apple 芯片的 Mac：

1. 如果系统提示你登录，请使用之前在这台 Mac 上使用的那个 Apple 账户登录。
2. 当设置助理打开时，使用它来完成 Mac 的设置。
6. 搭载 T2 芯片的 Mac：

1. 恢复的 Mac 会在通过互联网从“macOS 恢复”启动时显示一个旋转的地球。当系统提示你选取语言时，请按提示操作。
2. 如果系统提示你登录，请使用之前在这台 Mac 上使用的那个 Apple 账户登录。然后点按“退出到恢复”。
3. 当你在“恢复”中看到实用工具列表时，请选取用于[安装或重新安装 macOS](https://support.apple.com/zh-cn/102655#reinstall) 的选项。
4. macOS 安装完成后，Mac 会重新启动并打开设置助理。使用设置助理来完成 Mac 的设置。

设置好电脑后，如果“访达”或 Apple Configurator 中没有显示受影响的 Mac：

- 如果你使用“访达”进行修复或恢复，请从菜单栏中选取“访达”>“设置”。点按设置窗口顶部的“边栏”，并确保“CD、DVD 和 iOS 设备”已被选中。
- 从受影响的 Mac 上拔下 USB-C 连接线，然后按住电源按钮最多 10 秒钟以将受影响的 Mac 关机。将 USB-C 连接线重新连接到 [DFU 端口](https://support.apple.com/zh-cn/120694)，然后再次尝试进入 DFU 模式。把握好按下按键的正确时机很重要。
- 尝试使用其他 USB-C 连接线。连接线必须同时支持数据传输和充电。

如果你无法完成修复或恢复，或者你仍需要协助，请[联系 Apple 支持](https://getsupport.apple.com/?caller=kbase)。

尝试从 App Store [下载 Apple Configurator](https://apps.apple.com/cn/app/apple-configurator/id1037126344) 时，如果 App Store 提示由于需要更高版本的 macOS 而无法安装 Apple Configurator，请确保你使用的是 macOS Monterey 12.4 或更高版本，并且[已登录 App Store](https://support.apple.com/guide/app-store/sign-in-fir6253293d/mac)。然后再试一次。接下来，系统应该会提示你下载较旧的 Apple Configurator 兼容版本。请允许下载，然后使用较旧的版本。

\* 如果你使用网页代理或防火墙，则它必须允许从 Mac 到 Apple 网络 (17.0.0.0/8) 的网络流量。如果你不确定，请查阅你的路由器手册或咨询互联网提供商。进一步了解有关[在企业网络上使用 Apple 产品](https://support.apple.com/zh-cn/101555)的信息。

发布日期： 2024 年 10 月 30 日