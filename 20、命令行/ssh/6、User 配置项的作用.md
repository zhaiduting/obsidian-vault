假设 `$HOME/.ssh/config` 文件含有以下配置

```yaml
Host github.com
    IdentityFile ~/.ssh/github_ed25519
    User xxx
```

那么执行git相关命令会发生什么？其中 `User xxx` 这行到底有啥用？

### 当明确指定连 `git@github.com` 时

例如执行命令 `git clone git@github.com:zhaiduting/obsidian-vault.git` 时，配置项中的 `User xxx` 不起任何作用，因为 clone 命令中已经明确指明了 User 为 git 了。

再如执行测试连接的命令 `ssh -T git@github.com` 时，同样不受 User 配置项的影响。

### 命令行删除 `git@` 后

此时 `User xxx` 才会有作用。例如以下命令

```shell
~ > ssh -T github.com
xxx@github.com: Permission denied (publickey).
```

虽然连接失败了，但这让我明白了 `User xxx` 配置项的作用。

### 如果没有 User 配置项

删掉 User 配置项后 `$HOME/.ssh/config` 文件内容如下

```yaml
Host github.com
    IdentityFile ~/.ssh/github_ed25519
```

此时执行两次测试连接的命令

```shell
~ > ssh -T github.com
zdt@github.com: Permission denied (publickey).
~ > 
~ > ssh -T git@github.com
Hi zhaiduting! You've successfully authenticated, but GitHub does not provide shell access.
```

第一条命令为什么会出现 `zdt@` 呢？当连接命令中没有指明用户名，并且配置文件中也没有出现 User 配置项时，就会使用电脑里当前用户的名称作为ssh命令中的用户名。我的电脑当前登录的是zdt这个用户，于是ssh命令里就出现了 `zdt@github.com` 了。

第二条命令明确指定 `git@github.com` 了，毫无疑问会成功。

### 结论

可以放心删除 `$HOME/.ssh/config` 文件中的 `User git` 这行配置，通常不会出现什么问题。真有问题的话，在命令中加上 `git@` 后便可解决此类问题。
