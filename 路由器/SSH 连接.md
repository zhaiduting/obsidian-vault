路由器需要先开启 SSH 登录

1. 浏览器打开 http://192.168.50.1/Advanced_System_Content.asp
2. 输入路由器的用户名 `zhaiduting` 及密码
3. 系统管理 » 系统设置 » 滚动页面到「服务」栏，完成下列设置后点击应用按钮：
   - 启用 SSH 为 LAN only
   - 不启用 SSH Port Forwarding
   - 改掉默认的端口号
   - 允许使用密码登录

Mac 可以使用 Termius 连接路由器，设置方法：

- Address 填写 192.168.50.1
- SSH on 哪个端口号？填上之前改掉的端口号
- 路由器的用户名、密码
  ![Termius连路由器](https://lib.zhaiduting.work.gd/uPic/Termius%E8%BF%9E%E8%B7%AF%E7%94%B1%E5%99%A8.png)
