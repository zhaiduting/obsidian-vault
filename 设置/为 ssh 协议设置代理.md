以下为 ~/.ssh/config 文件内容，其中 github.com 走了代理，而 gitee.com 无需走代理。

```
Host github.com
    AddKeysToAgent yes
    UseKeychain yes
    IdentityFile ~/.ssh/github_ed25519
    ProxyCommand nc -vx 192.168.50.1:55555 -X 5 ssh.%h 443

Host gitee.com
    AddKeysToAgent yes
    UseKeychain yes
    IdentityFile ~/.ssh/gitee_ed25519
```

代理工具安装在路由器里，其中 192.168.50.1 正是路由器的 ip 地址，而 55555 则是 Socks 端口号，可以打开 http://192.168.50.1:9990/ui/zashboard/#/settings 更改端口号（临时有效）。下图终端敲入命令`git push`后的输出结果说明配置已生效。
![[为ssh协议设置代理.png]]
要想永久更改端口号，可以通过编辑 .yaml 文件实现，末尾加入一行 ==socks-port: 55555==

### ssh 设置代理的关键配置项

```
ProxyCommand nc -vx 192.168.50.1:55555 -X 5 ssh.%h 443
```

### 各部分解析

#### 1. ProxyCommand

- 这是 SSH 配置文件 (`~/.ssh/config`) 中的一个选项，表示 SSH 在连接远程主机时，不是直接建立连接，而是通过 `ProxyCommand` 指定的命令来代理访问。

#### 2. nc（netcat）

- `nc`（Netcat）是一个通用的网络工具，常用于测试端口、建立 TCP/UDP 连接等。它也可以用于创建代理隧道。
- 在这里，`nc` 用来通过 SOCKS5 代理服务器 **192.168.50.1:55555** 转发 SSH 连接。

#### 3. `-v`

- `-v`（verbose）表示启用详细模式，方便调试，会打印连接过程中的详细信息，例如 `Connection to github.com port 22 [tcp/ssh] succeeded!`。

#### 4. `-x 192.168.50.1:55555`

- `-x` 选项用于指定 **SOCKS 代理服务器** 的地址。
- `192.168.50.1:55555` 表示代理服务器运行在 **192.168.50.1**（通常是路由器）上的 **55555** 端口。

#### 5. `-X 5`

- `-X` 选项指定代理类型：
    - `4` 表示 **SOCKS4 代理**。
    - `5` 表示 **SOCKS5 代理**（这里使用 `-X 5`，说明连接是通过 SOCKS5 代理）。

#### 6. `ssh.%h 443`

- `%h` 是 SSH 变量，表示 **目标主机名**（例如 `github.com`）。
- `ssh.%h` 代表 `ssh.github.com`（有些服务提供不同的 SSH 端点，例如 GitHub）。
- `443` 是端口号，通常 **SSH 默认使用 22 端口**，但某些代理环境可能只允许 443 端口（通常用于 HTTPS），因此这里使用 443 端口进行 SSH 连接。

### 完整流程

1. SSH 连接时不会直接访问 `github.com`。
2. `nc` 先连接到 **192.168.50.1:55555**（SOCKS5 代理）。
3. `nc` 通过 SOCKS5 代理 **转发 SSH 连接** 到 `ssh.github.com:443`（GitHub 提供的 SSH 端口）。
4. SSH 通过 `ProxyCommand` 进行代理通信，而不是直接连接 GitHub。
