将 [`docker-mirror-gen.sh`](0、Containers/Docker/9%20docker-mirror-gen.sh.md) 生成的镜像配置文件保存到用户的 `.config/containers` 文件夹下

```sh
# 用户级
mkdir -p ~/.config/containers
cp registries.conf ~/.config/containers/registries.conf
```

然后执行以下命令测试镜像文件是否被读取

```sh
podman --log-level debug pull alpine
```

### 解决 DNS 解析超时问题 (I/O Timeout)

如果路由器的 DNS 使用了 Fake ip 模式，可能会导致 podman 拉取镜像失败，首先确认：

- 虚拟机的网络使用桥接模式
- 虚拟机使用自动生成的 Mac 地址

然后：

1. merlin clash 的高级模式里「开启http/socks代理端口」，保存并重启 clash；
2. 重启后发现 merlin clash 处于关闭状态，并且无法打开；
3. 于是返回高级模式里「关闭http/socks代理端口」保存并重启
4. 开启 merlin clash

这么一折腾，发现 centos 可以拉取镜像了。为什么会这样？
