将 [`docker-mirror-gen.sh`](0、Containers/Docker/9%20docker-mirror-gen.sh.md) 生成的镜像配置文件 registries.conf 保存在 Mac 的 `$HOME/.config/containers/` 文件夹下，但是 podman 拉取镜像的时候并不会读取这份配置文件。

原因是 podman 是运行在虚拟机里面的，它不会读取宿主机里的 registries.conf 文件。可以采用软链接解决这个问题，让虚拟机里的 `~/.config/containers/registries.conf` 指向宿主机的 `$HOME/.config/containers/registries.conf` 文件就可以了。

可以采用以下命令创建这个软链接

```sh
podman machine ssh "ln -snf $HOME/.config/containers/registries.conf ~/.config/containers/registries.conf"
```

至此，镜像配置完毕，在 Mac 中使用 Podman 拉取镜像时应该会提速。也可以通过 ssh 连接到虚拟机采用调试模式拉取镜像，看看配置文件到底有没有生效。

```sh
Last login: Mon Dec 22 11:56:29 on ttys000

# Mac 终端连接虚拟机
~ > podman machine ssh
Connecting to vm podman-machine-default. To close connection, use `~.` or `exit`
Fedora CoreOS 43.20251110.3.1
Tracker: https://github.com/coreos/fedora-coreos-tracker
Discuss: https://discussion.fedoraproject.org/tag/coreos

Last login: Mon Dec 22 11:48:44 2025 from 192.168.127.1
core@localhost:~$

# 验证软链接
core@localhost:~$ ll ~/.config/containers/registries.conf
lrwxrwxrwx. 1 core core 45 Dec 22 11:48 /var/home/core/.config/containers/registries.conf -> /Users/zdt/.config/containers/registries.conf
core@localhost:~$

# 在调试模式下拉取 alpine 镜像
core@localhost:~$ podman --log-level debug pull alpine
INFO[0000] podman filtering at log level debug
DEBU[0000] Called pull.PersistentPreRunE(podman --log-level debug pull alpine)
INFO[0000] Setting parallel job count to 19
DEBU[0000] Using conmon: "/usr/bin/conmon"
INFO[0000] Using sqlite as database backend
DEBU[0000] Using graph driver overlay
DEBU[0000] Using graph root /var/home/core/.local/share/containers/storage
DEBU[0000] Using run root /run/user/501/containers
DEBU[0000] Using static dir /var/home/core/.local/share/containers/storage/libpod
DEBU[0000] Using tmp dir /run/user/501/libpod/tmp
DEBU[0000] Using volume path /var/home/core/.local/share/containers/storage/volumes
DEBU[0000] Using transient store: false
DEBU[0000] [graphdriver] trying provided driver "overlay"
DEBU[0000] Cached value indicated that overlay is supported
DEBU[0000] Cached value indicated that overlay is supported
DEBU[0000] Cached value indicated that metacopy is not being used
DEBU[0000] Cached value indicated that native-diff is usable
DEBU[0000] backingFs=xfs, projectQuotaSupported=false, useNativeDiff=true, usingMetacopy=false
DEBU[0000] Initializing event backend journald
DEBU[0000] Configured OCI runtime crun-vm initialization failed: no valid executable found for OCI runtime crun-vm: invalid argument
DEBU[0000] Configured OCI runtime runc initialization failed: no valid executable found for OCI runtime runc: invalid argument
DEBU[0000] Configured OCI runtime runj initialization failed: no valid executable found for OCI runtime runj: invalid argument
DEBU[0000] Configured OCI runtime kata initialization failed: no valid executable found for OCI runtime kata: invalid argument
DEBU[0000] Configured OCI runtime ocijail initialization failed: no valid executable found for OCI runtime ocijail: invalid argument
DEBU[0000] Configured OCI runtime runsc initialization failed: no valid executable found for OCI runtime runsc: invalid argument
DEBU[0000] Configured OCI runtime youki initialization failed: no valid executable found for OCI runtime youki: invalid argument
DEBU[0000] Configured OCI runtime krun initialization failed: no valid executable found for OCI runtime krun: invalid argument
DEBU[0000] Using OCI runtime "/usr/bin/crun"
DEBU[0000] Pulling image alpine (policy: always)
DEBU[0000] Looking up image "alpine" in local containers storage
DEBU[0000] Normalized platform linux/arm64 to {arm64 linux  [] }

# 以下表明 Podman 读取了软链接指向的配置文件
DEBU[0000] Loading registries configuration "/var/home/core/.config/containers/registries.conf"
DEBU[0000] Trying "localhost/alpine:latest" ...
DEBU[0000] reference "[overlay@/var/home/core/.local/share/containers/storage+/run/user/501/containers]localhost/alpine:latest" does not resolve to an image ID
DEBU[0000] Trying "docker.io/library/alpine:latest" ...
DEBU[0000] reference "[overlay@/var/home/core/.local/share/containers/storage+/run/user/501/containers]docker.io/library/alpine:latest" does not resolve to an image ID
DEBU[0000] Trying "docker.io/library/alpine:latest" ...
DEBU[0000] reference "[overlay@/var/home/core/.local/share/containers/storage+/run/user/501/containers]docker.io/library/alpine:latest" does not resolve to an image ID
DEBU[0000] Trying "alpine" ...
DEBU[0000] Normalized platform linux/arm64 to {arm64 linux  [] }
DEBU[0000] Attempting to pull candidate docker.io/library/alpine:latest for alpine
DEBU[0000] parsed reference into "[overlay@/var/home/core/.local/share/containers/storage+/run/user/501/containers]docker.io/library/alpine:latest"
DEBU[0000] Resolving "alpine" using unqualified-search registries (/var/home/core/.config/containers/registries.conf)
Resolving "alpine" using unqualified-search registries (/var/home/core/.config/containers/registries.conf)
Trying to pull docker.io/library/alpine:latest...
DEBU[0000] Copying source image //alpine:latest to destination image [overlay@/var/home/core/.local/share/containers/storage+/run/user/501/containers]docker.io/library/alpine:latest
DEBU[0000] Using registries.d directory /etc/containers/registries.d

# Podman 并没有直接去连接官方的 `docker.io`，而是优先访问了配置文件的 docker.1ms.run 镜像站
DEBU[0000] Trying to access "docker.1ms.run/library/alpine:latest"
DEBU[0000] No credentials matching docker.1ms.run/library/alpine found in /run/user/501/containers/auth.json
DEBU[0000] No credentials matching docker.1ms.run/library/alpine found in /var/home/core/.config/containers/auth.json
DEBU[0000] No credentials matching docker.1ms.run/library/alpine found in /var/home/core/.docker/config.json
DEBU[0000] No credentials matching docker.1ms.run/library/alpine found in /var/home/core/.dockercfg
DEBU[0000] No credentials for docker.1ms.run/library/alpine found
DEBU[0000]  No signature storage configuration found for docker.1ms.run/library/alpine:latest, using built-in default file:///var/home/core/.local/share/containers/sigstore
DEBU[0000] Looking for TLS certificates and private keys in /etc/docker/certs.d/docker.1ms.run
DEBU[0000] GET https://docker.1ms.run/v2/
DEBU[0003] Ping https://docker.1ms.run/v2/ status 401
DEBU[0003] GET https://docker.1ms.run/openapi/v1/auth/token?scope=repository%3Alibrary%2Falpine%3Apull&service=docker.1ms.run
DEBU[0006] GET https://docker.1ms.run/v2/library/alpine/manifests/latest
DEBU[0006] Content-Type from manifest GET is "application/vnd.oci.image.index.v1+json"
DEBU[0006] Using SQLite blob info cache at /var/home/core/.local/share/containers/cache/blob-info-cache-v1.sqlite
DEBU[0006] Source is a manifest list; copying (only) instance sha256:410dabcd6f1d53f1f4e5c1ce9553efa298ca6bcdd086dfc976b8f659d58b46d2 for current system
DEBU[0006] GET https://docker.1ms.run/v2/library/alpine/manifests/sha256:410dabcd6f1d53f1f4e5c1ce9553efa298ca6bcdd086dfc976b8f659d58b46d2
DEBU[0006] Content-Type from manifest GET is "application/vnd.oci.image.manifest.v1+json"
DEBU[0006] IsRunningImageAllowed for image docker:docker.io/library/alpine:latest
DEBU[0006]  Using default policy section
DEBU[0006]  Requirement 0: allowed
DEBU[0006] Overall: allowed
DEBU[0006] Downloading /v2/library/alpine/blobs/sha256:e8f9ca9f1870bc194d961e259fd1340c641bf188e0d02e58b86b86445a4bc128
DEBU[0006] GET https://docker.1ms.run/v2/library/alpine/blobs/sha256:e8f9ca9f1870bc194d961e259fd1340c641bf188e0d02e58b86b86445a4bc128

# 加速器（docker.1ms.run）在返回数据时没有提供文件总大小的 HTTP 头部，但并不会影响下载
DEBU[0009] failed to get blob size: Missing "Content-Length" header in response
Getting image source signatures
DEBU[0009] Reading /var/home/core/.local/share/containers/sigstore/library/alpine@sha256=410dabcd6f1d53f1f4e5c1ce9553efa298ca6bcdd086dfc976b8f659d58b46d2/signature-1
DEBU[0009] Not looking for sigstore attachments: disabled by configuration
DEBU[0009] Manifest has MIME type application/vnd.oci.image.manifest.v1+json, ordered candidate list [application/vnd.oci.image.manifest.v1+json, application/vnd.docker.distribution.manifest.v2+json, application/vnd.docker.distribution.manifest.v1+prettyjws, application/vnd.docker.distribution.manifest.v1+json]
DEBU[0009] ... will first try using the original manifest unmodified
DEBU[0009] Checking if we can reuse blob sha256:f6b4fb9446345fcad2db26eac181fef6c0a919c8a4fcccd3bea5deb7f6dff67e: general substitution = true, compression for MIME type "application/vnd.oci.image.layer.v1.tar+gzip" = true
DEBU[0009] Failed to retrieve partial blob: no TOC found and convert_images is not configured
DEBU[0009] Downloading /v2/library/alpine/blobs/sha256:f6b4fb9446345fcad2db26eac181fef6c0a919c8a4fcccd3bea5deb7f6dff67e
DEBU[0009] GET https://docker.1ms.run/v2/library/alpine/blobs/sha256:f6b4fb9446345fcad2db26eac181fef6c0a919c8a4fcccd3bea5deb7f6dff67e
Copying blob f6b4fb944634 [----------------------------] 0.0b / 4.0MiB | 0.0 b/s
DEBU[0010] failed to get blob size: Missing "Content-Length" header in response
DEBU[0010] Detected compression format gzip
Copying blob f6b4fb944634 done   |
DEBU[0011] Cached value indicated that idmapped mounts for overlay are not supported
DEBU[0011] Check for idmapped mounts support
DEBU[0011] Applying tar in /var/home/core/.local/share/containers/storage/overlaCopying blob f6b4fb944634 done   |
DEBU[0011] No compression detected
DEBU[0011] Compression change for blob sha256:e8f9ca9f1870bc194d961e259fd1340c641bf188e0d02e58b86b86445a4bc128 ("application/vnd.oci.image.config.v1+json") not supported
DEBU[0011] Using original blob without modification
Copying config e8f9ca9f18 done   |
Writing manifest to image destination
DEBU[0011] setting image creation date to 2025-12-18 00:12:28.695990232 +0000 UTC
DEBU[0011] created new image ID "e8f9ca9f1870bc194d961e259fd1340c641bf188e0d02e58b86b86445a4bc128" with metadata "{}"
DEBU[0011] added name "docker.io/library/alpine:latest" to image "e8f9ca9f1870bc194d961e259fd1340c641bf188e0d02e58b86b86445a4bc128"

# 拉取成功！
DEBU[0011] Pulled candidate docker.io/library/alpine:latest successfully
DEBU[0011] parsed reference into "[overlay@/var/home/core/.local/share/containers/storage+/run/user/501/containers]@e8f9ca9f1870bc194d961e259fd1340c641bf188e0d02e58b86b86445a4bc128"
DEBU[0011] exporting opaque data as blob "sha256:e8f9ca9f1870bc194d961e259fd1340c641bf188e0d02e58b86b86445a4bc128"
e8f9ca9f1870bc194d961e259fd1340c641bf188e0d02e58b86b86445a4bc128
DEBU[0011] Called pull.PersistentPostRunE(podman --log-level debug pull alpine)
DEBU[0011] Shutting down engines
INFO[0011] Received shutdown.Stop(), terminating!        PID=2133
core@localhost:~$
core@localhost:~$ exit
logout
~ >
```

在 Debug 模式下，Podman 会记录每个动作的**时间戳**（即每行开头的 `DEBU[XXXX]`），我们可以通过这些时间点推算出下载的平均速度。

日志中关于数据传输的关键节点如下：

- 开始下载镜像层（Layer）：
  `DEBU[0009] GET https://docker.1ms.run/v2/library/alpine/blobs/sha256:f6b4fb94...`
- 完成下载并开始应用（Apply）：
  `DEBU[0011] Applying tar in /var/home/core/.local/share/containers/storage/...`

**推算过程：**

1. **耗时**：从 `0009` 秒开始到 `0011` 秒结束，下载过程大约持续了 **2 秒**。
2. **文件大小**：日志中提到 `Copying blob f6b4fb944634 ... 4.0MiB`。
3. **计算**：$4.0\text{ MiB} \div 2\text{ 秒} = 2.0\text{ MiB/s}$。

所以，镜像加速后的下载速度大约在 **2MB/s** 左右。
