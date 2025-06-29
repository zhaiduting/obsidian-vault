把 `$HOME/go/bin` 添加到 `PATH` 环境变量中：

```shell
export PATH=$PATH:$HOME/go/bin
```

作用：让你可以在命令行中直接运行 Go 安装的工具和命令。

### 举个例子

假设在 GoLand 的终端敲入以下命令安装了 air

```shell
go install github.com/air-verse/air@latest
```

之后就可以执行 air 的相关命令了

```shell
goblog2 > which air
/Users/zdt/go/bin/air
goblog2 > air -v

  __    _   ___
 / /\  | | | |_)
/_/--\ |_| |_| \_ v1.61.7, built with Go go1.24.1

goblog2 > air

  __    _   ___
 / /\  | | | |_)
/_/--\ |_| |_| \_ v1.61.7, built with Go go1.24.1

watching .
watching bootstrap
watching http
watching http/controllers
watching http/middleware
watching models
watching pkg
watching resources
!exclude resources/compiled_views
watching resources/views
watching resources/views/articles
watching routes
watching scripts
watching tests
!exclude tmp
> bash ./compile-views.sh
building...
running...
```

如果关闭 GoLand 改用终端执行 air 命令的话就会报错，如下

```shell
Last login: Fri Jun 27 12:07:05 on ttys000
~ > cd Code/goblog2
goblog2 > air
zsh: command not found: air
goblog2 >
```

### 解决方法

编辑 ~/.zshrc 文件，加入以下设置

```shell
export PATH=$PATH:$HOME/go/bin
```

重新开启终端后再次执行 air 命令就没问题了。除了 air 之外，对于使用 `go install` 命令安装的其他工具同理。这便是添加 Go 工具到 PATH 变量中的作用。
