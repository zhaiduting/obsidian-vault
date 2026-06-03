即使启用了 sudo 也不能顺利执行命令，例如

```sh
PS C:\Users\zhaid> sudo Set-Service -Name ssh-agent -StartupType Automatic
找不到命令
PS C:\Users\zhaid>
```

解决方法：

- 放弃 `sudo`
- 改用 `gsudo`

先安装 `gsudo`

```sh
winget install gsudo
```

然后改用以下命令

```sh
gsudo Set-Service -Name ssh-agent -StartupType Automatic
```

![gsudo命令](https://lib.zhaiduting.work.gd/uPic/gsudo%E5%91%BD%E4%BB%A4.jpg)
