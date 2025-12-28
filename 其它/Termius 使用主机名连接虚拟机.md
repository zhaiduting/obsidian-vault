Termius 是一款支持全平台的 SSH 客户端，能像浏览器开标签页一样同时连接、操作多台服务器。安装命令为：

```sh
brew install --cask termius
```

Termius 连接服务器，通常要知道服务器的 IP 地址。如果服务器是本地机器上的虚拟机，可以通过虚拟机的主机名进行连接。

### 对于「桥接网络」的虚拟机

Address 里直接填写：虚拟机的主机名称

![Termius](https://lib.zhaiduting.work.gd/uPic/Termius.png)

可以在宿主机里敲入以下命令进行验证

```sh
nslookup centos <路由器的IP>

# 也可以不写路由器地址，就像下面这样
nslookup centos
```

如果能显示虚拟机的 IP 地址，类似下面这样，通常 Termius 可以正常连接虚拟机。

```sh
Name:	centos
Address: 192.168.50.153
```

#### 解决网关问题

如果仍然无法连接，并且宿主机的终端出现类似如下所示的输出

```powershell
# 不指定路由器 IP 时无法识别 centos 主机
~ > nslookup centos
Server:		114.114.114.114
Address:	114.114.114.114#53

** server can't find centos: NXDOMAIN

# 直接询问路由器却能识别
~ > nslookup centos 192.168.50.1
Server:		192.168.50.1
Address:	192.168.50.1#53

Name:	centos
Address: 192.168.50.163
```

解决方法：将宿主机网络设置界面的 DNS 服务器更改为路由器的 IP 地址。

#### 解决失效的问题

过了一段时间，发现 Termius 又不上虚拟机。宿主机终端 nslookup 命令显示如下信息

```sh
~ > nslookup centos
Server:		192.168.50.1
Address:	192.168.50.1#53

** server can't find centos: NXDOMAIN
```

解决方法：重新开启一下虚拟机的桥接网络。

### 对于「共享网络」的虚拟机

Address 里需要填写：虚拟机的主机名.local

![centos.local](https://lib.zhaiduting.work.gd/uPic/centos.local.png)
不仅如此，虚拟机里应该先安装 avahi 并进行相关配置。终端记录如下

```sh
# 安装 avahi
~ > sudo dnf install -y avahi
[sudo] wo 的密码：
CentOS Stream 10 - BaseOS             726  B/s | 4.2 kB     00:05
CentOS Stream 10 - AppStream          673  B/s | 4.3 kB     00:06
CentOS Stream 10 - Extras packages    823  B/s | 5.3 kB     00:06
软件包 avahi-0.9~rc2-2.el10.aarch64 已安装。
依赖关系解决。
无需任何处理。
完毕！

# 设置防火墙
~ > sudo systemctl enable --now avahi-daemon
~ > sudo firewall-cmd --permanent --add-service=mdns
success
~ > sudo firewall-cmd --reload
success

# 查询主机名
~ > hostname
centos
```

这段代码的作用是开启 **mDNS（多播 DNS）** 服务。它的目的是让你的 CentOS 像苹果设备一样，在局域网内“广播”自己的名字，这样你就不需要记 IP 地址，直接通过 `主机名.local` 就能连接。

以下是每行代码的详细解释：

1. **`sudo yum install -y avahi`**: 安装 `Avahi` 软件包。Avahi 是 Linux 下对苹果 Bonjour 协议（mDNS）的开源实现。
2. **`sudo systemctl enable --now avahi-daemon`**: 立即启动 Avahi 服务，并设置开机自启动。
3. **`sudo firewall-cmd --permanent --add-service=mdns`**: 在 CentOS 的防火墙中永久放行 mDNS 协议所需的端口（UDP 5353）。如果不执行这一步，Mac 发出的探测信号会被 CentOS 挡掉。
4. **`sudo firewall-cmd --reload`**: 重新加载防火墙配置，使刚才的改动立即生效。

将查询到的主机名称添上 `.local` 后写到 Termius 的地址栏里就可以连接虚拟机了。这种连接方式对于「桥接模式」的虚拟机同样有效。
