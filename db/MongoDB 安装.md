此数据库既可位于本地又可位于云端，对应的安装方式也相应地分为两类。

### 本地安装

mac 系统安装 MongoDB 的命令如下，其他系统请参 [官方文档的安装说明](https://www.mongodb.com/docs/manual/administration/install-community/)

```shell
brew tap mongodb/brew
brew install mongodb-community
```

安装后敲入命令启动服务，并设置为开机自启动

```shell
brew services start mongodb/brew/mongodb-community
```

### 云端

如果使用云端数据库，那本机只需安装云端数据库的管理工具。相关命令如下，更多信息可参考 [官方文档说明](https://www.mongodb.com/try/download/community)

```shell
brew install mongodb-atlas
atlas setup
```
