`ssh-agent` 是一个**密钥管理器**，它用来在内存中保存你的 SSH 私钥，这样你就不需要每次连接远程服务器或推送 Git 仓库时都输入密码或私钥的 passphrase。

### 优点

- 避免频繁输入私钥密码。
- 私钥只保存在本地内存中，不会暴露在磁盘或命令行参数中。
- 和 `ssh`、`git` 等工具无缝配合。

### 工作原理

- 你先启动 `ssh-agent`。它会在后台运行，并设置一些环境变量（主要是 `SSH_AUTH_SOCK` 和 `SSH_AGENT_PID`）。
- 然后用 `ssh-add` 把私钥加载到 `ssh-agent` 中。
- 之后，每次你用 `ssh` 或 `git` 需要认证时，客户端会通过 `ssh-agent` 去使用已加载的私钥完成认证，而不是让你手动输入密码。

#### 常用命令

```bash
# 启动 ssh-agent
eval "$(ssh-agent -s)"

# 添加私钥（会要求输入一次密码）
ssh-add ~/.ssh/id_rsa

# 查看已加载的密钥
ssh-add -l

# 删除所有密钥
ssh-add -D
```
