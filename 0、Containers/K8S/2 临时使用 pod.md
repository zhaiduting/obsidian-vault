### 临时使用 pod

`kubectl run` —— 专门为“临时 Pod / 调试”设计 ⭐

```sh
kubectl run debug --image=busybox -it --rm -- sh
```

这会：

- 创建 **一个 Pod**
- 不创建 Deployment / ReplicaSet
- 退出 pod 后自动销毁

注意：当 Pod 内部发生错误（非人为退出）时，pod 不会被销毁。要想创建一个更安全的、真正一次性的 pod，可以改用以下命令

```sh
# 多了 --restart 选项
kubectl run debug --image=busybox -it --rm --restart=Never -- sh
```
