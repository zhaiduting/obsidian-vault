### 临时使用 pod

`kubectl run` —— 专门为“临时 Pod / 调试”设计 ⭐
执行以下任意命令一条命令

```sh
kubectl run debug --image=busybox -it --rm -- sh

# 或者用以下命令
kubectl run debug --image=busybox -it --rm

# 以下皆因缺少 -it 报错
# kubectl run debug --image=busybox --rm ❌
# kubectl run debug --image=busybox --rm -- sh ❌
```

将会：

- 创建 **一个 Pod**
- 不创建 Deployment / ReplicaSet
- 退出 pod 后自动销毁

注意：当 Pod 内部发生错误（非人为退出）时，pod 不会被销毁。要想创建一个更安全的、真正一次性的 pod，可以改用以下命令

```sh
# 多了 --restart 选项
kubectl run debug --image=busybox -it --rm --restart=Never -- sh
```

### 新手陷阱

```sh
# 以下导致 k8s 无限重启 pod 问题
# kubectl run debug --image=busybox ⚠️

# 以下皆因缺少 -it 导致命令失败
# kubectl run debug --image=busybox --rm ❌
# kubectl run debug --image=busybox --rm -- sh ❌

# 正确命令如下
kubectl run debug --image=busybox --rm -it
```

详情：[⚠️ pod 无限重启问题](0、Containers/K8S/⚠️%20pod%20无限重启问题.md)

### 可省略 `-- sh` 的情况

对于 busybox 而言，它的 Dockerfile 默认指令就是 `sh`，因此可以省略。也就是说以下两种写法实际效果一样

```sh
kubectl run debug --image=busybox --rm -it -- sh
kubectl run debug --image=busybox --rm -it
```

## 对比 podman 的 run 命令

区别在于：

- 使用 --name 命名容器
- 删掉 --image=
- 需将 --rm 置于镜像之前

```sh
podman run --name debug --rm busybox # ✅
# 以下错误命令
# podman run debug --image=busybox --rm ❌
```
