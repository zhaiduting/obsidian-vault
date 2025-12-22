![安装podman](https://lib.zhaiduting.work.gd/uPic/%E5%AE%89%E8%A3%85podman.png)
由于红帽（Red Hat）家族（包括 CentOS）目前主推自家的容器工具 **Podman**，因此官方仓库里默认只提供 Podman，而没有 Docker。直接执行 `dnf install docker` 命令的话，会因为找不到软件包而报错。

由于 Podman 是 Docker 的一个直接替代品，它的命令和 Docker 几乎一模一样，且无需守护进程（Daemonless），安全性更高。因此我就执行了以下命令准备试用 podman

```sh
sudo dnf install -y podman
```

并且安装了 `podman-docker`，这玩意可以让 docker 命令生效。如果不装这玩意的话，所有 docker 命令都得改用 podman 命令。
