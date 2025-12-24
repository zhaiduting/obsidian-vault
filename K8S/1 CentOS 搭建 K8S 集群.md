### 首先安装 Docker 或者 Podman

前文已述

### 安装 Kind

官方文档 https://kind.sigs.k8s.io/docs/user/quick-start/#installing-from-release-binaries
命令如下

```sh
# For AMD64 / x86_64
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.31.0/kind-linux-amd64
# For ARM64
[ $(uname -m) = aarch64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.31.0/kind-linux-arm64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

### 创建默认集群

以下命令会采用 kind 的默认配置创建一个名为 `dev-cluster` 单节点集群，此节点既是控制平面又是工作节点。

```sh
kind create cluster --name dev-cluster
```

执行结果如下

```sh
~ > kind create cluster --name dev-cluster
enabling experimental podman provider
Creating cluster "dev-cluster" ...
 ✓ Ensuring node image (kindest/node:v1.35.0) 🖼
 ✓ Preparing nodes 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
Set kubectl context to "kind-dev-cluster"
You can now use your cluster with:

kubectl cluster-info --context kind-dev-cluster

Have a nice day! 👋
```

可以通过以下命令获取节点信息

```sh
kubectl get nodes
```

结果报错，提示 `kubectl` 命令不存在。

### 安装 kubectl

官方文档 https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

#### 下载命令

```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/arm64/kubectl"
```

#### 安装

```sh
# 1. 赋予执行权限
chmod +x ./kubectl

# 2. 移动到系统可搜索的目录（这样你才能在任何地方直接敲 kubectl）
sudo mv ./kubectl /usr/local/bin/kubectl

# 3. 验证是否成功
kubectl version --client
```

此时再次执行 `kubectl get nodes` 就不会报错了，终端记录如下

```sh
~ > chmod +x ./kubectl
~ > sudo mv ./kubectl /usr/local/bin/kubectl
[sudo] zdt 的密码：
~ > kubectl version --client
Client Version: v1.35.0
Kustomize Version: v5.7.1
~ >
~ > kubectl get nodes
NAME                        STATUS   ROLES           AGE    VERSION
dev-cluster-control-plane   Ready    control-plane   109m   v1.35.0
```
