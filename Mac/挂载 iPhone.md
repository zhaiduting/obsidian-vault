方法有多种，本文记录其中之一：通过 Finder 开启 Wi-Fi 隔空管理。

这种方法可以让 iPhone 在不插线的情况下，出现在 Mac Finder 的左侧边栏，虽然它不像 U 盘那样能看到所有层级，但它是管理 **App 沙盒文件**（如 Obsidian 库）的标准方式。

### 首次激活（需插线一次）

![挂载iPhone](https://lib.zhaiduting.work.gd/uPic/%E6%8C%82%E8%BD%BDiPhone.png)

- 用线连接 iPhone 到 Mac。
- 在 Finder 侧边栏选中 iPhone，在右侧“常规”选项卡勾选：**“连接 Wi-Fi 时显示此 iPhone”**。
- 点击“应用”并断开数据线。

### 无线挂载

- 只要两台设备在同一 Wi-Fi，iPhone 就会一直出现在 Finder 侧边栏。
- 点击 iPhone -> 选择顶部菜单栏的 **“文件” (Files)**。
- 你会看到支持文件共享的 App 列表（如 Obsidian）。你可以直接把文件夹从 Mac 桌面拖进这里的 App 图标上。

### 拖拽传输

![拖拽传输到iPhone](https://lib.zhaiduting.work.gd/uPic/%E6%8B%96%E6%8B%BD%E4%BC%A0%E8%BE%93%E5%88%B0iPhone.png)

在 macOS 的访达（Finder）中通过无线或有线方式连接 iPhone 后，由于 iOS 的**沙盒机制**，你无法像操作普通 U 盘那样在 iPhone 的根目录或任意位置直接“右键创建文件夹”。

只能在支持“文件共享”的 App（例如 **Obsidian**、**PlayerXtreme** 等）的目录下操作。步骤如下：

1. 在 Finder 侧边栏选中你的 iPhone。
2. 点击右侧顶部的 **“文件” (Files)** 选项卡。
3. 你会看到一个 App 列表。**注意：** 在这个界面下，你无法直接通过右键创建文件夹。
4. **正确做法**：在你的 Mac 本地（比如桌面）先建好文件夹，命名好，然后将**整个文件夹**直接拖拽到 Finder 列表中的某个 App（如 Obsidian）上方。
5. 这样，这个文件夹及其内部所有内容都会被拷贝进 iPhone 对应的 App 沙盒里。
