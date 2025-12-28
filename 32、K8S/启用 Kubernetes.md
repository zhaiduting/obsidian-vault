![启用Kubernetes](https://lib.zhaiduting.work.gd/uPic/%E5%90%AF%E7%94%A8Kubernetes.png)
Docker Desktop 设置界面启用 K8S 的步骤如下：

- 打开 Enable Kubernetes 开关
- 点选 kind，选择版本，设定 Node 数量
- 点击 Apply 按钮，之后只需等待下载 kindest 镜像。等到 Cluster 的状态为 Running 时，K8S 的安装过程就完成了。

### 验证集群状态

```sh
~ > kubectl version --client
Client Version: v1.34.1
Kustomize Version: v5.7.1
~ >
~ > kubectl cluster-info
Kubernetes control plane is running at https://127.0.0.1:50643
CoreDNS is running at https://127.0.0.1:50643/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
~ >
~ > kubectl get nodes
NAME                    STATUS   ROLES           AGE   VERSION
desktop-control-plane   Ready    control-plane   11m   v1.31.1
desktop-worker          Ready    <none>          11m   v1.31.1
desktop-worker2         Ready    <none>          11m   v1.31.1
~ >
```
