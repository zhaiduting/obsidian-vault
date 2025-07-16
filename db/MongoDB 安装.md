MongoDB 是一个 NoSQL 数据库，其名称来源于单词 “humongous”（意为“巨大的”）。此数据库既可位于本地又可位于云端，对应的安装方式也相应地分为两类。

mongo 音标：/ˈmɒŋɡoʊ/（美式英语）或 /ˈmɒŋɡəʊ/（英式英语）

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

### 可视化管理工具

MongoDB Compass 是 MongoDB 官方提供的免费 GUI 工具，适合开发者、管理员和初学者，用于管理本地或云端的 MongoDB 数据库（包括 MongoDB Atlas）。

```shell
brew install --cask mongodb-compass
```
