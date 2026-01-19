在 **containerd 2.0+** 中，官方不推荐在主配置文件中硬编码镜像列表，而是推荐 **"Configuration Directory"** 模式。这种模式更整洁：它在 `/etc/containerd/certs.d/` 下为每个镜像仓库（如 `docker.io`）创建一个独立的文件夹和配置文件。

此模式又称 `config_path` 模式，或者 Hosts 模式。配置文件的绝对路径类似于下面这样：

```sh
# 通项表达式：
/etc/containerd/certs.d/<host_name>/hosts.toml

# 具体例子：
# /etc/containerd/certs.d/docker.io/hosts.toml
# /etc/containerd/certs.d/quay.io/hosts.toml
```

### 动态映射的优点

1. **热更新**：以后想增加或删除加速器，只需修改宿主机的 `hosts.toml`。**无需重启 KinD 集群**，containerd 会在下次拉取时自动读取新配置。
2. **更强的表达力**：每个加速器（Host）都可以独立配置权限、认证信息或是否跳过 TLS 验证。
3. **符合未来趋势**：这是 `containerd 2.x` 乃至以后版本的标准做法，老的 `mirrors` 字段最终会被完全废弃。

### 使用 `extraMounts` 实现动态映射

要在 KinD 中实现这种模式，我们需要配合使用 `extraMounts`（将宿主机配置挂载进去）或者 `containerdConfigPatches`（在初始化时生成路径）。

这种方法最优雅，因为它能让你直接在**宿主机**上管理镜像配置，KinD 节点会自动同步。

#### 1. 在宿主机创建配置目录

在你的电脑上建立一个符合 containerd 2.0 规范的结构：

```sh
mkdir -p ./kind-certs/docker.io
```

#### 2. 创建 `hosts.toml` 文件

在 `./kind-certs/docker.io/hosts.toml` 中写入你的镜像加速器：

```sh
server = "https://docker.io"

[host."https://docker.1ms.run"]
  capabilities = ["pull", "resolve"]

[host."https://docker.m.daocloud.io"]
  capabilities = ["pull", "resolve"]

[host."https://registry.cyou"]
  capabilities = ["pull", "resolve"]
```

#### 3. 修改 `kind-config.yaml`

```yaml
# 一主两从的 3 节点配置文件

kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraMounts: &certs_mount  # 定义锚点名为 certs_mount
  - hostPath: ./kind-certs
    containerPath: /etc/containerd/certs.d
- role: worker
  extraMounts: *certs_mount  # 引用锚点
- role: worker
  extraMounts: *certs_mount  # 引用锚点

containerdConfigPatches:
- |-
  [plugins."io.containerd.grpc.v1.cri".registry]
    config_path = "/etc/containerd/certs.d"
```

在配置文件中做两件事：

1. 开启 `config_path` 补丁。
2. 将刚才创建的目录挂载到节点容器内。

如果 `containerdConfigPatches` 配置失效，可能遇到了 `containerd` 的版本问题。参考 https://github.com/containerd/containerd/blob/main/docs/hosts.md 查找相关版本的有效配置

### 验证镜像配置是否有效

开两个终端，并且都连接到同一个 kind-worker 节点。一个终端监测 containerd 的日志变化，然后在另一个终端拉取一个很小的 alpine 镜像。根据 containerd 日志的输出信息可以判断 registry 镜像站是否生效。

两个终端都要敲入以下命令连接到同一个节点，因为后续命令都需要在节点的 sh 窗口内敲入。

```sh
podman exec -it kind-worker sh
```

首先排除干扰因素，在任意一个 sh 里执行以下命令

```sh
# 查看目录结构是否符合 Containerd 规范
ls -R /etc/containerd/certs.d/

# 删除之前已下载的 alpine 镜像
crictl rmi alpine
```

然后在左侧终端敲入监测命令

```sh
# 监测 containerd 日志变化，忽略之前所有信息
journalctl -u containerd -f --since "1 second ago"
```

在右侧终端拉取镜像

```sh
# 故意拉取一个不存在的版本，看看是否走了镜像站
# 如果能看到 host=<主机名称> 之类的输出，说明配置有效
crictl pull alpine:888

# 实际拉取一下，看看耗时多少
crictl pull alpine
```

![containerd验证镜像站](https://lib.zhaiduting.work.gd/uPic/containerd%E9%AA%8C%E8%AF%81%E9%95%9C%E5%83%8F%E7%AB%99.png)
最后，右侧终端敲入 `exit` 命令退出 kind-worker 节点，再执行 `kubectl` 命令运行一个 `nginx` 容器。看看拉取镜像的过程是否有加速效果

```sh
# 建议先计时，并指定一个不带本地缓存的版本
kubectl run nginx-test --image=nginx:latest --restart=Never

# 测试完成后，记得删除这个 Pod 以释放资源
kubectl delete pod nginx-test
```

通过新增的 kind-worker2 节点的 `containerd` 的监测日志，得到以下关键数据：

- **拉取镜像耗时**：`Pulled image "nginx:latest" ... in 20.154219926s`
- **读取字节量**：`bytes read=60913232` (约 **58.1 MB**)

**计算平均下载速度**：大约 **2.88 MB/s**。

**结论**：在没有加速器的情况下，从国内直连 Docker Hub 拉取 60MB 的镜像通常需要数分钟甚至直接超时失败。**20 秒完成拉取，说明加速器有效。**

### 常见配置列表总结

根据需要可在 `/etc/containerd/certs.d/` 下分别建立以下目录，并将与之对应的国内镜像站的主机名称分别写进各自的 `hosts.toml` 文件里。

| **仓库域名**            | **常见内容**                                |
| ----------------------- | ------------------------------------------- |
| `docker.io`             | 大多数公共应用镜像 (Nginx, Redis, MySQL 等) |
| `registry.k8s.io`       | K8s 核心组件、Pause 镜像 (必配)             |
| `quay.io`               | Prometheus, Operator SDK 相关, CoreOS 组件  |
| `ghcr.io`               | GitHub 托管的各种云原生工具                 |
| `gcr.io` / `k8s.gcr.io` | Google 云相关镜像及旧版 K8s 镜像            |
| `nvcr.io`               | NVIDIA GPU 驱动插件、深度学习镜像           |

### 解决监视器句柄数量超限问题

如果碰到错误提示 **"Insufficient watch descriptors available"**，解决方法请参考：[监视器句柄数量超限问题](Bug/监视器句柄数量超限问题.md)
