原因是在 Fake-ip 模式下 CentOS 的 podman 无法拉取镜像，而 Ubuntu 却不存在这个问题。

### 具体来说是这样的：

- 路由器的 DNS 方案设置为 Fake-ip 模式
- 虚拟机网络设置为桥接模式
- 虚拟机终端执行命令 `podman --log-level debug pull alpine`

在 CentOS 上，这条命令经常出现类似下面这样的问题

```sh
# 192.168.50.179 是 centos 虚拟机 IP
# 192.168.50.1 是路由器的 IP

DEBU[0020] Accessing "registry-1.docker.io/library/alpine:latest" failed: pinging container registry registry-1.docker.io: Get "https://registry-1.docker.io/v2/": dial tcp: lookup registry-1.docker.io on 192.168.50.1:53: read udp 192.168.50.179:56819->192.168.50.1:53: i/o timeout
```

在 Ubuntu 上，这条命令可以正常拉取镜像。另外，在宿主机上，这条命令也可以正常执行。

### 结论

[CentOS Stream 10](https://www.centos.org/download/) 这个系统对 DNS 的 Fake-ip 支持不友好，改用 [Ubuntu](https://ubuntu.com/download) 避免这个问题。
