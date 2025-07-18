MongoDB 是一个 NoSQL 数据库，其名称来源于单词 “humongous”（意为“巨大的”）。此数据库既可位于本地又可位于云端，对应的安装方式也相应地分为两类。

mongo 音标：/ˈmɒŋɡoʊ/（美式英语）或 /ˈmɒŋɡəʊ/（英式英语）

### macOS 安装 MongoDB

#### 本地安装

mac 系统安装 MongoDB 的命令如下，其他系统请参 [官方文档的安装说明](https://www.mongodb.com/docs/manual/administration/install-community/)

```shell
brew tap mongodb/brew
brew install mongodb-community

# 安装命令如果省略了版本号，则会安装最新版
```

安装后敲入命令启动服务，并设置为开机自启动

```shell
brew services start mongodb/brew/mongodb-community
```

详细说明可参考 [Install on macOS](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-os-x/)

#### 云端

如果使用云端数据库，那本机只需安装云端数据库的管理工具。相关命令如下，更多信息可参考 [官方文档说明](https://www.mongodb.com/try/download/community)

```shell
brew install mongodb-atlas
atlas setup
```

#### 可视化管理工具

MongoDB Compass 是 MongoDB 官方提供的免费 GUI 工具，适合开发者、管理员和初学者，用于管理本地或云端的 MongoDB 数据库（包括 MongoDB Atlas）。

```shell
brew install --cask mongodb-compass
```

### Ubuntu 安装 MongoDB

安装命令为

```shell
sudo apt install mongodb-org
```

如果命令报错 <font color="#C01C28"><b>E: </b></font>Unable to locate package mongodb-org 那是因为找不到软件包，需要**手动添加 MongoDB 官方源**之后才能安装。步骤如下

#### 1️⃣ 导入 MongoDB 的 GPG 签名密钥

```shell
curl -fsSL https://www.mongodb.org/static/pgp/server-8.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg \
   --dearmor
```

**作用：**

- 保证你从 MongoDB 官方源下载的包是经过签名验证的，防止中间人攻击（MITM）。
- Debian 系列系统使用 `gpg` 验证 `.deb` 包的来源是否合法。

#### 2️⃣ 添加 MongoDB 的官方软件源

```shell
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] https://repo.mongodb.org/apt/ubuntu noble/mongodb-org/8.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list
```

**作用：**

- 告诉 `apt` 到 MongoDB 官方维护的源中查找 `mongodb-org` 这个软件包。
- 默认情况下，Ubuntu 软件源不包含 MongoDB 的最新版（官方版本），所以必须添加。

#### 3️⃣ 更新本地的软件包索引

```shell
sudo apt-get update
```

**作用：**

- 让系统重新读取所有源的信息（包括你刚刚添加的 MongoDB 官方源），
- 否则，`apt` 根本不知道 `mongodb-org` 是哪里来的。

#### 4️⃣ 安装 MongoDB

```shell
sudo apt-get install -y mongodb-org
# 使用 apt-get 或 apt 都可以安装
```

**作用：**

- 这才是真正安装 MongoDB 所有组件的步骤。
- 但它依赖前面 3 步确保你能从正确的源中找到、验证并下载软件包。

#### 为什么不能直接跳到第 4 步？

省略前 3 个步骤中的任何一步都无法正常安装

| 步骤                | 没有执行的后果                                    |
| ------------------- | ------------------------------------------------- |
| 第 1 步（密钥）     | 即使能找到包，也可能因为 GPG 校验失败而拒绝安装。 |
| 第 2 步（源）       | 根本找不到 `mongodb-org` 包。                     |
| 第 3 步（更新索引） | 找不到新加源中的包。                              |
