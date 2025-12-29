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

以下命令会采用 kind 的默认配置创建一个默认名称为 kind 的单节点集群

```sh
kind create cluster

# 相当于执行以下命令
# kind create cluster --name kind

# 或者改用以下命令创建一个名称为 dev-cluster 的集群
# kind create cluster --name dev-cluster
```

执行结果如下

```sh
zdt@centos:~$ # For AMD64 / x86_64
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.31.0/kind-linux-amd64
# For ARM64
[ $(uname -m) = aarch64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.31.0/kind-linux-arm64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    97  100    97    0     0     17      0  0:00:05  0:00:05 --:--:--    21
  0     0    0     0    0     0      0      0 --:--:--  0:00:10 --:--:--     0
100  9.9M  100  9.9M    0     0   593k      0  0:00:17  0:00:17 --:--:-- 2282k
[sudo] zdt 的密码：
zdt@centos:~$ kind get clusters
enabling experimental podman provider
No kind clusters found.

# 创建第一个集群
zdt@centos:~$ kind create cluster
enabling experimental podman provider
Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.35.0) 🖼
 ✓ Preparing nodes 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
Set kubectl context to "kind-kind"
You can now use your cluster with:

kubectl cluster-info --context kind-kind

Not sure what to do next? 😅  Check out https://kind.sigs.k8s.io/docs/user/quick-start/
zdt@centos:~$

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
zdt@centos:~$ kubectl get nodes
bash: kubectl: 未找到命令...
zdt@centos:~$
zdt@centos:~$ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/arm64/kubectl"
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   138  100   138    0     0     26      0  0:00:05  0:00:05 --:--:--    30
100 52.6M  100 52.6M    0     0  3587k      0  0:00:15  0:00:15 --:--:-- 11.3M
zdt@centos:~$
zdt@centos:~$ # 1. 赋予执行权限
chmod +x ./kubectl

# 2. 移动到系统可搜索的目录（这样你才能在任何地方直接敲 kubectl）
sudo mv ./kubectl /usr/local/bin/kubectl

# 3. 验证是否成功
kubectl version --client
[sudo] zdt 的密码：
对不起，请重试。
[sudo] zdt 的密码：
Client Version: v1.35.0
Kustomize Version: v5.7.1
zdt@centos:~$
zdt@centos:~$ kubectl get nodes
NAME                 STATUS   ROLES           AGE   VERSION
kind-control-plane   Ready    control-plane   13m   v1.35.0
zdt@centos:~$
```
