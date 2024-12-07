将U盘中的 .ssh.zip 文件解压到用户目录，解压后的文件夹如下图。共有3份密钥，分别对应于github、gitee和imooc这3个网站的。
![[我的 .ssh 文件夹.png]]
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