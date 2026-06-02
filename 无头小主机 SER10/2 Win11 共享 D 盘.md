目标：从 Mac 直接读写 Windows 的 D 盘

Win11 已经共享了 D 盘，权限设为可读可写，但是 Mac 的网络里面怎么也找不到 Window 电脑。解决步骤如下

### Win11 创建新访客

```sh
net user macshare 123456 /add
```

### Mac 访达连接服务器

假设 Win11 的电脑名称为 SER10，服务器地址就是 `smb://SER10`
用户名：macshare
密码：123456
![smb连接ser10](https://lib.zhaiduting.work.gd/uPic/smb%E8%BF%9E%E6%8E%A5ser10.png)

### 解决断连问题

Mac 和 Win11 都要关闭睡眠，可能需要用到以下命令（一个查询 Smb 服务配置，另一个设置若干秒后自动断连）
- `Get-SmbServerConfiguration`
- `Set-SmbServerConfiguration -AutoDisconnectTimeoutInSecondsV2 0 -Force`