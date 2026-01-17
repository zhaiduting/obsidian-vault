![crictl](https://lib.zhaiduting.work.gd/uPic/crictl.png)

`crictl` 是一个**面向容器运行时的调试/运维工具**。使用此命令之前，先连接集群的一个工作节点。以 podman 连接 kind-work 为例，终端记录如下

```sh
~ > kubectl get node
NAME                 STATUS   ROLES           AGE   VERSION
kind-control-plane   Ready    control-plane   33m   v1.35.0
kind-worker          Ready    <none>          33m   v1.35.0
kind-worker2         Ready    <none>          33m   v1.35.0
~ >
~ > podman exec -it kind-worker bash
root@kind-worker:/#
```

### 镜像相关命令

```sh
crictl images       # 查看镜像列表
crictl pull <image> # 拉取 alpine 镜像
crictl rmi <image>  # 删除镜像
```

### 其它命令

```sh
#列出 PodSandbox（= 节点上的 Pod）
crictl pods

# 列出容器
crictl ps

# 查看容器日志（非常常用）
crictl logs <container_id>

# 进入容器（debug 神器）
crictl exec -it <container_id> sh

# 查看 Pod / 容器详细信息
crictl inspectp <pod_id>
crictl inspect <container_id>
```

### 什么时候「一定要用 crictl」？

- kubelet 报错、apiserver 连不上
- Pod 卡在 `ContainerCreating`
- 镜像拉不下来，想看真实错误
- kind / k3s / 裸机节点排错
- 使用 containerd（没有 docker）
