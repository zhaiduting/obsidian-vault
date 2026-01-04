在 Kubernetes (K8s) 中，设置 Pod 的节点亲和性（Node Affinity）主要是为了将 Pod 调度到特定的节点上。它比传统的 `nodeSelector` 更灵活，支持逻辑运算（如 In, NotIn, Exists 等）。

节点亲和性分为两种类型：**硬限制**（强制执行）和**软限制**（优先考虑）。

### 硬限制 (Required)

**全称：** `requiredDuringSchedulingIgnoredDuringExecution`

如果节点不满足条件，Pod **将不会**被调度，直到有匹配的节点出现。

**使用场景：** 必须运行在特定的硬件（如 GPU）或特定的可用区。

以下配置要求 Pod 只能被调度到固态硬盘节点上。准确来说，节点必须含有 `disktype=ssd` 这个标签。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: affinity-pod-hard
spec:
  containers:
  - name: nginx
    image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
```

### 软限制 (Preferred)

**全称：** `preferredDuringSchedulingIgnoredDuringExecution`

调度器会**尝试**寻找匹配的节点，如果找不到，它仍然会将 Pod 调度到其他不满足条件的节点上。

**使用场景：** 尽量分布在某个区域，但如果该区域满了，允许在别处运行以保证可用性。

以下配置期望 Pod 被优先调度到东1区的节点上。准确来说，调度器在给节点打分时，会给带有 `region=east-1` 标签的节点额外加 1 分（因为设定了 `weight: 1`）。在实际生产中，如果你非常希望它去 `east-1`，建议把权重设高一些（比如 **80** 或 **100**）。注意：即使权重为 100，它依然只是一个“强烈建议”，而不是“强制指令”。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: affinity-pod-soft
spec:
  containers:
  - name: nginx
    image: nginx
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1  # 权重 1-100，权重越高优先级越高
        preference:
          matchExpressions:
          - key: region
            operator: In
            values:
            - east-1
```

### ~ During Scheduling Ignored During Execution

这串冗长的名字其实是 Kubernetes 官方为了精准描述调度行为而采用的“大白话”，它包含两个阶段：

- **During Scheduling（在调度期间）**：指 Pod 还没有被分配到节点，调度器（Scheduler）正在决定它该去哪里的过程。
- **Ignored During Execution（在执行期间被忽略）**：指 Pod 已经运行在某个节点上之后。如果此时节点的标签（Label）发生了变化，不再满足亲和性要求了，系统**不会**把这个 Pod 踢走（驱逐）。

**一句话总结：** 只在找房子的（调度）时候提要求，住进去（运行）之后就算环境变了也不搬家。

### 注意事项：Key 必须存在

在实际操作中，这两个配置生效的前提是**节点上必须已经打好了相应的标签**。

- **对于硬限制**：如果你忘记给节点打 `disktype=ssd`，你的 Pod 会永远 Pending。
- **对于软限制**：如果你忘记给节点打 `region=east-1`，调度器会发现所有节点的这一项得分都是 0，于是它会退化成普通调度逻辑（只看 CPU、内存等因素）。

### 应用场景

必须使用【硬限制】的情况：

- **硬件依赖**：你的程序必须用 GPU 才能启动。
- **合规/隔离**：法律规定数据必须存储在特定的物理区域。
- **确定性**：你宁愿 Pod 不启动，也不希望它跑在错误的节点上。

**使用【软限制 + 权重 100】的情况：**

- **性能优化**：在 SSD 上跑得快，但在机械硬盘上也能跑。
- **成本控制**：优先用包年包月的节点，实在不行再开按需计费的节点。
- **容灾**：尽量跨机房分布，但如果只剩一个机房活着，那就全部挤在那。

### 如何验证它们生效了？

当你部署后，可以用以下命令快速排查：

**查看 Pod 状态及调度到了哪个节点：**

```sh
kubectl get pod -o wide
```

**如果 Pod Pending，查看原因：**

```sh
kubectl describe pod affinity-pod-hard
# 在 Events 部分你会看到类似 "0/3 nodes are available: 3 node(s) didn't match node selector." 的提示
```

**查看节点标签是否正确：**

```sh
kubectl get nodes --show-labels
```
