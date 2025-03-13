将U盘中的 .ssh.zip 文件解压到用户目录，解压后的文件夹如下图。共有3份密钥，分别对应于github、gitee和imooc这3个网站的。
![](我的%20.ssh%20文件夹.png)
由于我的钥匙串之前存储了3份密钥的密码，所以当我打开Mac终端敲入命令`git clone git@github.com:zhaiduting/zdt`后会立即克隆仓库。

但是Window没有钥匙串，所以需要多走以下步骤：
1. 设置ssh-agent服务类型为自动开启，并手动开启一次，命令为
```shell
Set-Service -Name ssh-agent -StartupType Automatic
Start-Service ssh-agent
```
2. 敲入以下代码添加密钥的密码
```shell
ssh-add $HOME/.ssh/github_ed25519
ssh-add $HOME/.ssh/gitee_ed25519
ssh-add $HOME/.ssh/imooc_rsa
```

设置后的window就可以在PowerShell或者GitBash中直接敲入`git clone git@github.com:zhaiduting/zdt`克隆仓库了。 

### 相关命令

```shell
ssh-add -l
ssh-add -D
```

### 解决一个Bug

Win11_24H2_Chinese_Simplified_Arm64 系统的 ssh-agent 无法记住密码，这是因为有多个 OpenSSH 导致的。终端敲入命令`choco uninstall git`将Git卸载，这也会将 Git 捆绑的 OpenSSH 一并卸载。然后访问 [git-scm.com](https://git-scm.com/downloads/win) 手动下载并安装Git，注意：安装过程中不要安装捆绑的 OpenSSH（如下图）

![](安装Git时不要安装捆绑的OpenSSH.jpg)
