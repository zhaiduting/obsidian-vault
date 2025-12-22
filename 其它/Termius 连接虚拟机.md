Termius 是一款支持全平台的 SSH 客户端，能像浏览器开标签页一样同时连接、操作多台服务器。安装命令为：

```sh
brew install --cask termius
```

### 连接设置

连接远程机器时，Address 里通常要写 IP 地址。但是连接虚拟机时，建议使用设备名称而不是设备的 IP 地址，因为这可以避免因 IP 变化导致连接失败的问题。

![Termius](https://lib.zhaiduting.work.gd/uPic/Termius.png)

### 为啥可以通过设备名称进行连接？

估计跟虚拟机的网络类型，以及物理路由器的功能相关。

#### 虚拟机设置为桥接模式

桥接的好处是：可以让虚拟机像真实电脑一样上网。

#### 路由器会记录上网设备的名称

可以通过以下命令进行验证

```sh
nslookup centos <路由器的IP>

# 也可以不写路由器地址，就像下面这样
nslookup centos
```

这条命令的大致意思是：查询一下名为 centos 的设备的 IP 地址是多少。结果显示：

```sh
Name:	centos
Address: 192.168.50.153
```

因此，Termius 连接 centos 虚拟机时，实际上是从路由器那里得到了虚拟机的 IP 地址。由此引入一个问题：如果遇到不够智能的路由器，通过设备名称进行连接的方式会不会失效呢？

### 注意

如果 Termius 无法连接虚拟机，并且宿主机的终端出现类似如下所示的输出

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

### 解决失效的问题

过了一段时间，发现 Termius 连不上虚拟机。宿主机终端 nslookup 命令显示如下信息

```sh
~ > nslookup centos
Server:		192.168.50.1
Address:	192.168.50.1#53

** server can't find centos: NXDOMAIN
```

解决方法：重新开启一下虚拟机的桥接网络。 这可以让虚拟机向路由器重新做一下“自我介绍”，之后 Ternius 就可以正常连接虚拟机了。
