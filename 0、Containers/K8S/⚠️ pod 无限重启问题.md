---
source: https://gemini.google.com/app/9b1762343d5abdc3
author:
  - Gemini
created: 2026-01-01
tags:
  - clippings
---

![pod无限重启问题](https://lib.zhaiduting.work.gd/uPic/pod%E6%97%A0%E9%99%90%E9%87%8D%E5%90%AF%E9%97%AE%E9%A2%98.png)
开两个终端，一个终端执行 `kubectl get pods -w` 持续查看 pod 状态。另一个执行以下命令

```sh
# 缺少 -it 和 --rm
kubectl run debug --image=busybox
```

为啥 pods 信息一直在变？后台一直在创建 pod 吗？

### 1\. 为什么会这样？（根本原因）

1. **Busybox 默认行为** ：当你运行 `kubectl run debug --image=busybox` 时，你没有给它指定运行命令。 `busybox` 镜像的默认入口通常是 `sh` 。
2. **立即退出** ：因为你没有提供交互式终端（如 `-it` ）， `sh` 发现没有输入任务，就会立即执行完毕并退出。
3. **退出码 0** ：在你的 `describe` 信息中可以看到 `Exit Code: 0` 。这意味着容器“正常运行结束”了。
4. **默认重启策略 (Restart Policy)** ：Pod 的默认重启策略是 `Always` 。
   - **K8s 的逻辑** ：既然你告诉我要“始终运行”这个 Pod，而它现在结束了，那我就得把它再拉起来。
5. **CrashLoopBackOff** ：当容器在短时间内反复退出又重启，Kubernetes 为了保护服务器不被频繁的启动进程拖垮，会增加重启之间的等待时间，这就是你看到的 `CrashLoopBackOff` 状态。

---

### 2\. 状态变化拆解

你的操作记录完美展示了这个过程：

| 状态 (Status)         | 背后发生了什么                                                                     |
| --------------------- | ---------------------------------------------------------------------------------- |
| **ContainerCreating** | K8s 正在拉取镜像，准备启动容器。                                                   |
| **Completed**         | 容器启动了，执行完默认命令（sh），正常退出。                                       |
| **CrashLoopBackOff**  | K8s 发现容器退出了，尝试重启，但因为失败次数多了，正在“闭门思过”（等待时间增加）。 |
| **RESTARTS 增加**     | 注意看你的 `RESTARTS` 列，从 0 变成了 1、2、3... 这说明是同一个 Pod 在反复诈尸。   |

---

### 3\. 如何解决？

如果你只是想让这个 Pod 乖乖呆着让你能进去调试，你需要给它一个“永远不会结束”的任务，或者开启交互模式。

#### 方法 A：加上交互模式（最推荐）

如果你想直接进入容器操作：

Bash

```
kubectl run debug --image=busybox -it --rm -- /bin/sh
```

- `-it`: 分配一个交互式终端。
- `--rm`: 退出后自动删除 Pod（保持集群干净）。

#### 方法 B：让它执行一个长任务

如果你想让它在后台一直跑：

Bash

```
kubectl run debug --image=busybox -- /bin/sh -c "sleep 3600"
```

这样容器会执行休眠 1 小时的命令， `Status` 就会长时间保持为 `Running` 。

#### 方法 C：修改重启策略

如果你只希望它运行一次，运行完就结束（不再重启）： 你需要使用 YAML 或 `job` ，将 `restartPolicy` 设置为 `OnFailure` 或 `Never` 。

---

**你想尝试进入这个容器内部看看，还是只是想让它停止报错？**

---

不应该重启了，怎么让 Kubernetes 放弃重启？

---

要让 Kubernetes 停止这种“无限重启”的行为，有几种处理方式，取决于你是想 **彻底删除** 它，还是想 **修改它的规则** 。

### 1\. 立即停止并删除（最快的方法）

如果你只是实验性地运行了这个 Pod，现在不想要它了，直接删除它即可：

```sh
kubectl delete pod debug # 👍
```

一旦 Pod 被删除，Kubernetes 就不会再尝试重启其中的容器。

### 2\. 让它运行完就结束（修改重启策略）

你之前使用的 `kubectl run` 命令默认会给 Pod 设置 `restartPolicy: Always` （总是重启）。

如果你希望容器运行完命令后就保持在 `Completed` 状态，不再尝试重启，需要在创建时指定重启策略为 **Never** 。

**注意：** `kubectl run` 只能通过参数指定部分配置，要修改重启策略需要这样写：

Bash

```
kubectl run debug-once --image=busybox --restart=Never -- /bin/sh -c "echo 'Hello'; sleep 5"
```

- `--restart=Never`: 告诉 K8s 容器退出后（无论成功还是失败）都不要重启。
- 执行完后，你会看到状态变为 `Completed` ，但 `RESTARTS` 次数会保持为 `0` 。

### 3\. 如果是用 Deployment 或其他控制器创建的

如果你发现删除了 Pod 之后，后台又立刻弹出一个新的 Pod（名字后面带随机后缀，比如 `debug-7f55...`），说明你可能创建的是 **Deployment** 而不是单个 Pod。

这时候你需要删除整个控制器：

Bash

```
kubectl delete deployment <deployment名称>
```

### 总结

你之所以看到它在“不停地变”，是因为：

- **你想要的状态** ：运行一次。
- **Kubernetes 认为的状态** ：你让我运行这个 Pod，它退出了就是“故障”了，我必须把它救回来（重启）。

**建议：** 如果你是想进去调试，以后建议养成带 `-it` 参数的习惯：

Bash

```
kubectl run debug -it --image=busybox --rm --sh
```

`--rm` 参数非常有用，它能保证你退出 shell 的瞬间，Pod 也会被自动销毁，不会留下任何后台重启的“后遗症”。

### 缺少 `--rm` 的情况

```sh
# 缺少 --rm
kubectl run debug --image=busybox -it
```

使用 `-it` 但**不使用** `--rm`，Pod 变成了“打不死的僵尸”，会在后台无限重启。拆解：

- **容器退出**：当你输入 `exit`，容器里的主进程（`sh`）结束了。
- **Pod 依然存在**：因为你没加 `--rm`，Kubernetes 收到容器结束的信号后，会保留这个 Pod 对象（为了让你能看日志或排查原因）。
- **触发重启策略**：由于 `kubectl run` 默认的重启策略是 `Always`，Kubernetes 看到容器停了，会立即认为“任务还没完”，于是自动拉起一个新的容器。
- **状态变为 Running**：这就是为什么你 `get pod` 看到它是 `Running`，但 `RESTARTS` 变成了 **1**。

可以使用以下命令再次进入此容器

```sh
kubectl attach debug -it
```

如果不想要了，退出容器后必须执行以下删除命令，否则会导致 pod 无限重启的问题

```sh
kubectl delete pod debug
```
