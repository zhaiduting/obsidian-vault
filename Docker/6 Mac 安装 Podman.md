### 安装

```sh
brew install podman
```

### 初始化并启动 Podman 虚拟机

与 Linux 不同，macOS 无法直接运行容器内核，因此 Podman 必须通过一个轻量级的虚拟机来工作。首次使用 Podman，或者之前没创建过虚拟机，请运行：

```sh
podman machine init
podman machine start
```

### 检查现有虚拟机状态

```sh
podman machine ls
```

- 如果 **STATE** 显示为 `Stopped`，请执行 `podman machine start`。
- 如果列表为空，请回到第 1 步执行 `init`。

### 验证连接

虚拟机启动后，再次检查连接：

```sh
podman system connection list
```

确保有一个名为 `podman-machine-default*` 的连接，且后面带有星号（表示当前激活）。

### 创建一个容器

```sh
# 端口如果小于1024会出现权限问题
podman container run -dp 8080:80 nginx

# 以下命令应该返回 html 文档
curl localhost:8080
```
