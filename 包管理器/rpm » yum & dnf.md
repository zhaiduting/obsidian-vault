`yum` 和 `dnf` 都是基于 RPM 的 Linux 发行版（如 RHEL、CentOS、Fedora）中的包管理器工具，用于安装、更新、卸载软件包。`dnf` 是 `yum` 的继任者，从 Fedora 22 和 RHEL 8 开始取代了 `yum`。

下面是两者的对比：

| 特性             | `yum` (Yellowdog Updater Modified) | `dnf` (Dandified YUM)           |
| ---------------- | ---------------------------------- | ------------------------------- |
| **诞生时间**     | 2003 年左右                        | 2015 年左右（Fedora 22 起默认） |
| **是否仍在维护** | 仅限于旧系统（如 CentOS 7）维护    | 是，主流系统默认使用            |
| **性能**         | 较慢，依赖解析效率低               | 更快，内存使用更优              |
| **依赖处理**     | 有时会处理失败或安装不必要的包     | 更智能，处理更准确              |
| **插件系统**     | 有丰富插件                         | 插件系统更现代、兼容性更好      |
| **API**          | Python 2 的 API，不易维护          | 使用 Python 3 API，结构更清晰   |
| **命令兼容性**   | 旧命令标准                         | 兼容大部分 `yum` 命令           |
| **事务机制**     | 不支持回滚                         | 支持事务性操作，可回滚更新      |
| **软件源管理**   | `/etc/yum.repos.d/`                | 相同                            |

### 示例对比：

| 操作         | `yum` 命令          | `dnf` 命令                    |
| ------------ | ------------------- | ----------------------------- |
| 安装软件     | `yum install nginx` | `dnf install nginx`           |
| 更新系统     | `yum update`        | `dnf upgrade` 或 `dnf update` |
| 删除软件     | `yum remove nginx`  | `dnf remove nginx`            |
| 清除缓存     | `yum clean all`     | `dnf clean all`               |
| 列出可更新包 | `yum list updates`  | `dnf list updates`            |

### 小结：

- **dnf 是 yum 的升级版，功能更强大、性能更好**。
- 如果你在 **CentOS 7 或 RHEL 7** 之前的系统上，仍需使用 `yum`。
- 在 **RHEL 8+ / Fedora 22+ / CentOS 8+ / Rocky Linux / AlmaLinux** 上，推荐使用 `dnf`，因为 `yum` 已是一个 `dnf` 的软链接。

如需了解当前系统的实际使用情况，你可以运行：

`ls -l /usr/bin/yum`

如果看到类似输出：

`lrwxrwxrwx 1 root root 5 ... /usr/bin/yum -> dnf`

说明系统实际上已经使用的是 `dnf`。
