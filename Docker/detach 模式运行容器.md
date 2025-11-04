### 使用 -d 选项

```sh
obsidian-vault > docker container run -dp 80:80 --name nginx nginx
b04539e0673f0d8b3b2cdf1e0e78e1cc296af00dfae3d3e2aaa336ab49ec62c8
obsidian-vault > docker rm nginx
Error response from daemon: cannot remove container "nginx": container is running: stop the container before removing or force remove
obsidian-vault > docker rm nginx -f
nginx
obsidian-vault > docker container run -pd 80:80 --name nginx nginx
docker: invalid containerPort: d

Run 'docker run --help' for more information
obsidian-vault >
obsidian-vault >
obsidian-vault > docker container run -dp80:80 --name nginx nginx
8ea9a324df1dbce78bba01920ccca0ce224e3f1c94703b6d8a3a0d50b1aca812
obsidian-vault > docker container attach nginx
192.168.65.1 - - [04/Nov/2025:07:19:47 +0000] "GET / HTTP/1.1" 200 615 "-" "curl/8.7.1" "-"
2025/11/04 07:19:58 [notice] 1#1: signal 28 (SIGWINCH) received
2025/11/04 07:19:58 [notice] 1#1: signal 28 (SIGWINCH) received
2025/11/04 07:19:58 [notice] 1#1: signal 28 (SIGWINCH) received
2025/11/04 07:19:58 [notice] 1#1: signal 28 (SIGWINCH) received
2025/11/04 07:19:58 [notice] 1#1: signal 28 (SIGWINCH) received
2025/11/04 07:19:59 [notice] 1#1: signal 28 (SIGWINCH) received
```

以下为有效写法

```sh
# ✅
docker container run -d -p 80:80 nginx
docker container run -dp 80:80 nginx
docker container run -dp80:80 nginx
```

以下也对

```sh
# ✅
docker container run -p 80:80 -d nginx
docker container run -p80:80 -d nginx
```

但是下面的写法错误，因为 -p 与端口号之间不能有其它选项！

```sh
# ❌
docker container run -p -d 80:80 nginx
```

注意，**选项 (flags)** 必须在 **镜像名称** **之前**指定。在镜像名称之后的所有内容都会被视为要在容器内部执行的**命令**及其**参数**。所以下面的写法是错的

```sh
# ❌
docker container run nginx --name nginx -d -p 80:80
```

### 使用 attach 命令连接容器

敲入 `docker container attach <container_id>` 可将终端连接到运行中的容器

```sh
obsidian-vault > docker container run -dp 80:80 nginx
974ea42fd46f221a244263d76b02e2a424fde99292d8396ad14e6cc90311030c
obsidian-vault > docker container attach 97
```

然后新开一个终端，敲入命令 `curl 127.0.0.1` 访问网站。此时原先的终端就会显示 ngxin 的访问日志，类似于下面这样

```
192.168.65.1 - - [04/Nov/2025:07:59:44 +0000] "GET / HTTP/1.1" 200 615 "-" "curl/8.7.1" "-"
```

⚠️ 不推荐使用 `docker container attach` 命令
