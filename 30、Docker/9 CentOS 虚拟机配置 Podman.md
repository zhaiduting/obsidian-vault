将 [`docker-mirror-gen.sh`](30、Docker/9%20docker-mirror-gen.sh.md) 生成的镜像配置文件保存到用户的 `.config/containers` 文件夹下

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

如果路由器的 DNS 使用了 Fake ip 模式，可能会导致 podman 拉取镜像失败，解决办法：

- 虚拟机的网络使用桥接模式
- 虚拟机使用自动生成的 Mac 地址
- 路由器的 DNS 劫持入口设置为后置模式
