使用 `kubectl set image` 命令升级 Nginx 版本是 Kubernetes 中最常用的滚动更新（Rolling Update）方式。基本命令可以写成以下通项式：

```sh
kubectl set image <资源类型>/<资源名称> <容器名称>=<镜像名称>:<标签>
```

### 1. 通项式详解

| **参数项**              | **示例值**                 | **说明**                                                                    |
| ----------------------- | -------------------------- | --------------------------------------------------------------------------- |
| **`<资源类型>`**        | `deployment` (或 `deploy`) | 告诉 K8s 你要修改的对象类型（如 Deployment, StatefulSet, DaemonSet 等）。   |
| **`<资源名称>`**        | `nginx-deployment`         | 对应 YAML 里的 `metadata.name`。                                            |
| **`<容器名称>`**        | `nginx`                    | **关键点：** 这是 YAML 里 `spec.template.spec.containers` 列表下的 `name`。 |
| **`<镜像名称>:<标签>`** | `nginx:1.25.3`             | 你想要更换的目标镜像和版本号。                                              |

---

### 2. 为什么需要“容器名称”？

在一个 Pod 里，可以同时运行多个容器（即“边车模式” Sidecar）。如果你不指定容器名，K8s 就不知道该给哪个镜像升级。

举个例子：

假设你的 Pod 里有两个容器，一个叫 web-server，一个叫 log-collector。如果你只想升级 Web 服务：

```sh
kubectl set image deployment/my-app web-server=nginx:1.25.3
```

这时候 `log-collector` 的版本会保持不变。

---

### 3. 一次性升级多个容器（进阶用法）

如果你想在一个命令里把 Pod 里的两个镜像都换了，也可以直接连着写：

```sh
kubectl set image deployment/my-app \
  web-server=nginx:1.25.3 \
  log-collector=fluentd:v1.16
```

---

### 4. 技巧：如果你不记得“容器名称”怎么办？

你可以让 K8s 告诉你当前 Deployment 里的容器叫什么名字：

```sh
# 这种方式最直观，直接看 Containers 部分
kubectl describe deployment <资源名称> | grep -A 5 Containers
```

或者使用快捷命令获取：

```sh
kubectl get deployment <资源名称> -o jsonpath='{.spec.template.spec.containers[*].name}'
```
