在 **containerd 2.0+** 中，官方不推荐在主配置文件中硬编码镜像列表，而是推荐 **"Configuration Directory"** 模式，或称 `config_path` (Hosts 模式)。这种模式更整洁：它在 `/etc/containerd/certs.d/` 下为每个镜像仓库（如 `docker.io`）创建一个独立的文件夹和配置文件。

要在 KinD 中实现这种模式，我们需要配合使用 `extraMounts`（将宿主机配置挂载进去）或者 `containerdConfigPatches`（在初始化时生成路径）。

### 使用 `extraMounts` 实现动态映射

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

### 动态映射的优点

1. **热更新**：你以后想增加或删除加速器，只需修改宿主机的 `hosts.toml`。**不需要重启 KinD 集群**，containerd 会在下次拉取时自动读取新配置。
2. **更强的表达力**：每个加速器（Host）都可以独立配置权限、认证信息或是否跳过 TLS 验证。
3. **符合未来趋势**：这是 `containerd 2.x` 乃至以后版本的标准做法，老的 `mirrors` 字段最终会被完全废弃。

### 验证

#### 检查节点文件挂载是否正确

```sh
# 进入其中一个工作节点
# podman exec -it kind-worker bash
docker exec -it kind-worker bash

# 查看目录结构是否符合 Containerd 规范
ls -R /etc/containerd/certs.d/
```

