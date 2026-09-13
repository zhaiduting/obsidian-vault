**DNSExit Remote Access** 是由网络服务商 DNSExit 提供的一种内网穿透与远程接入服务（功能类似于 Cloudflare Tunnel、ngrok 或 frp）。

主要用于以下场景：

- **无公网 IP / CGNAT 环境：** 运营商没有分配公网 IPv4，或者处于多重 NAT 下。
- **无路由器权限 / 端口被封：** 无法在路由器做公网端口映射（Port Forwarding），或者运营商封锁了 80/443 等常用入站端口。
- **快速暴露本地服务：** 将内网运行的 Web 应用、NAS 或开发测试环境，通过分配的域名（如 `xxx.tunnel.dnsexit.com`）安全的暴露到公网供外部访问。

### 内网主机需要安装 frp

通过 [Scoop](../19、包管理器/win/Scoop.md) 安装 frp 的命令如下

```sh
scoop install main/frp
```

### 找到 DNSExit 提供的 `frpc.toml`

在 DNSExit 开通 Remote Access 服务后，会自动生成一份配置，内容类似下面这样

```toml
serverAddr = "remote1.dnsexit.com"
serverPort = 7000
user = "r888"
metadatas.token = "2abdcdjfeijkdjfekf890jfejfkek******efxyz1"

[[proxies]]
name = "web-888"
type = "http"
localIP = "127.0.0.1"
localPort = 8080
subdomain = "ra888"
```

将此配置保存到主机里，假设保存位置为 `C:\frp\frpc.toml`
之后在终端敲入以下命令就可以实现内网穿透

```sh
frpc -c C:\frp\frpc.toml
```

### 创建 frpc 服务

存在一个问题，终端窗口不能关闭，一关闭的话 frpc 也就终止服务了。解决办法就是创建一个开机启动的服务，本文采用 nssm 创建这个服务。首先通过 Scoop 安装 nssm，安装命令如下

```sh
scoop install main/nssm
```

然后在管理员终端通过 nssm 创建并启动 frpc 服务，这两条命令如下

```sh
nssm install frpc "C:\Users\xxx\scoop\apps\frp\current\frpc.exe" "-c C:\frp\frpc.toml"
nssm start frpc
```

至此，所有步骤均已完成，主机重启后仍然有效。

### 验证

方法一：访问 DNSExit 的 Remote Access Control Panel 并点击页面上的
`Check Connection` 链接进行验证。
方法二：访问 `https://ra888.tunnel.dnsexit.com/` 进行验证，这要求内网主机的对外服务运行正常。

### 常用维护命令

以后如果需要对 `frpc` 服务进行维护，直接在**管理员终端**使用这几条命令即可：

修改配置文件后重启服务

```sh
nssm restart frpc
```

查看运行状态

```sh
nssm status frpc
```

手动停止服务

```sh
nssm stop frpc
```

彻底卸载服务

```sh
nssm stop frpc
nssm remove frpc confirm
```

### 字母含义

这两个工具的名字首字母含义如下：

#### 1. FRP

- **全称**：**F**ast **R**everse **P**roxy
- **含义**：快速反向代理。它是一个高性能的反向代理应用，主要用于内网穿透（将内网服务安全暴露到公网上）。

#### 2. NSSM

- **全称**：**N**on-**S**ucking **S**ervice **M**anager
- **含义**：“不烂 / 不渣的软件服务管理器”。
- **背景**：这里的 “Non-Sucking” 带有极客式的幽默感。作者认为 Windows 自带的原生服务封装工具（如 `srvany.exe`）极其难用且容易出现进程挂死、无法正常响应停止等缺陷（很 “suck”），因此开发了这个轻量、稳定且不会挂掉的代替工具，并直白地命名为 NSSM。
