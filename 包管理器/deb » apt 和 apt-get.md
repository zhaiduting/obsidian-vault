`apt` 和 `apt-get` 都是 Ubuntu（以及其他基于 Debian 的 Linux 发行版）用来管理软件包的命令行工具，它们有紧密的关系，但用途略有不同：

## 🧬 关系与本质

- **`apt-get`** 是 **老牌工具**，历史悠久，功能强大，适用于脚本和自动化。
- **`apt`** 是 **较新的命令**（Ubuntu 16.04 起引入），是对 `apt-get` 和 `apt-cache` 的 **统一封装和简化**，更适合人类用户在终端交互使用。

它们都基于相同的底层库（APT：Advanced Package Tool），所以你可以理解为：

> **`apt` = 为人类用户设计的 `apt-get` + `apt-cache` 合并版界面**

## 🥊 区别对比

| 功能 / 命令  | `apt` 示例        | `apt-get` 对应命令     | 说明                                           |
| ------------ | ----------------- | ---------------------- | ---------------------------------------------- |
| 安装软件     | `apt install xxx` | `apt-get install xxx`  | ✅ 一样                                        |
| 升级所有包   | `apt upgrade`     | `apt-get upgrade`      | ✅ 一样                                        |
| 更新软件列表 | `apt update`      | `apt-get update`       | ✅ 一样                                        |
| 删除软件     | `apt remove xxx`  | `apt-get remove xxx`   | ✅ 一样                                        |
| 显示软件信息 | `apt show xxx`    | `apt-cache show xxx`   | ✅ 简化                                        |
| 搜索软件     | `apt search xxx`  | `apt-cache search xxx` | ✅ 简化                                        |
| 清除下载缓存 | `apt clean`       | `apt-get clean`        | ✅ 一样                                        |
| 安全自动升级 | 🚫                | `apt-get dist-upgrade` | ❌ apt 没有完全替代所有命令（如 dist-upgrade） |
