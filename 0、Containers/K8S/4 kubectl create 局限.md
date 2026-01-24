在创建“带有容器”的资源（如 Deployment）时，无法直接指定容器名称。而 [kubectl set image](./3%20kubectl%20set%20image.md) 命令又偏偏需要知道容器的名称，因为它使用的是 `<容器名称>=<镜像名称>:<标签>` 的形式。

**问题：使用 create deployment 创建的资源如何使用 set image 更新镜像呢？**

### 方法一：利用默认命名规则（镜像名）

当你执行 `kubectl create deployment nginx-app --image=nginx` 时：

1. **Deployment 的名称**是 `nginx-app`。
2. **容器的名称**（Container Name）实际上是 **`nginx`** 而不是 `nginx-app`⚠️

**默认规则是：** `kubectl` 会从你提供的 `--image` 参数中截取镜像的名字（去掉镜像仓库地址和 Tag）作为容器名。

- `--image=nginx` $\rightarrow$ 容器名为 `nginx`
- `--image=bitnami/nginx:latest` $\rightarrow$ 容器名为 `nginx`
- `--image=my-repo:5000/custom-app:v1` $\rightarrow$ 容器名为 `custom-app`

实际测试一下

```sh
~ > kubectl create deployment nginx-app --image=nginx:1.14.2
deployment.apps/nginx-app created

# 以下证明容器名称并非 nginx-app
~ > kubectl set image deployment/nginx-app nginx-app=nginx:1.21.0
error: unable to find container named "nginx-app"

# 正确的容器名称为 nginx
~ > kubectl set image deployment/nginx-app nginx=nginx:1.21.0
deployment.apps/nginx-app image updated
~ >
```

再次总结此规则：容器名称默认使用镜像名，而非部署的名称。

### 方法二：根据 describe 命令查询

这是获取容器名称的万能方法，例如

```sh
~ > kubectl describe deployment nginx-app | grep container -iA 4
  Containers:
   nginx:
    Image:         nginx:1.21.0
    Port:          <none>
    Host Port:     <none>
```

可以非常清晰地确认容器的结构：

- **`Containers:`** 下方缩进的第一行 `nginx:` 就是**容器名称**。
- **`Image:`** 后面跟着的是当前运行的镜像版本 `nginx:1.21.0`。

### 方法三：通过 JSONPath 动态获取名称（最自动化）

如果你处于脚本自动化场景，或者 Deployment 里有多个容器，不确定具体名称，可以用 `kubectl get` 配合 `jsonpath` 提取容器名，然后传递给 `set image`。

```sh
# 获取第一个容器的名称并存入变量
CONTAINER_NAME=$(kubectl get deployment nginx-app -o jsonpath='{.spec.template.spec.containers[0].name}')

# 使用变量进行更新
kubectl set image deployment/nginx-app ${CONTAINER_NAME}=nginx:latest
```

### 方法四：使用 "Dry Run" 生成配置文件

在生产环境中，直接用命令行修改镜像虽然快，但不够透明。你可以先生成一个带自定义容器名的 YAML 文件，再进行管理。

**操作步骤：**

1. **生成 YAML：** `kubectl create deployment nginx-app --image=nginx:1.14.2 --dry-run=client -o yaml > deploy.yaml`
2. **手动修改：** 在 `deploy.yaml` 中将 `containers.name` 修改为你喜欢的名字（如 `web-server`）。
3. **应用：** `kubectl apply -f deploy.yaml`
4. **后续更新：** 此时你就明确知道名字了： `kubectl set image deployment/nginx-app web-server=nginx:1.21.0`
