执行相关命令的前提是 MongoDB 服务已经正常启动了。

### 了解 services

如果是通过 homebrew 安装的，与服务相关的可用命令有以下几条。分别用于启动服务，重启服务，查看服务状态，关闭服务。

```shell
brew services start mongodb-community
brew services restart mongodb-community
brew services info mongodb-community
brew services stop mongodb-community
```

如果是通过 apt 安装的，对应的命令如下。注意，敲入 `systemctl status mongod` 命令后将会进入分页器界面（按字母 q 退出分页）。

```shell
sudo systemctl start mongod
sudo systemctl restart mongod
sudo systemctl status mongod
sudo systemctl stop mongod
```

### 连接数据库

如果未修改本地数据库的默认配置，并且没有设置密码，则可以直接使用如下命令进行连接。注意 `mongo` 命令在新版数据库中已被弃用，取而代之的是 `mongosh`

```
mongosh
```

假设 Ubuntu 虚拟机的 IP 地址为 192.168.50.221，在 mac 终端可以通过以下命令进行连接

```shell
mongosh "mongodb://192.168.50.221:27017"
```

如果虚拟机的 ip 地址是动态分配的，可以在网络设置界面将其修改成固定的。这与 mac 修改 ip 的方法很相似。对于没有图形界面的虚拟机，可通过 `nmcli con` 命令进行修改（内容略）。如果修改后网络无法连接，请将虚拟机的网络类型切换为**桥接网络**。

### 常用命令

使用 `mongosh` 成功连接 MongoDB 数据库后，可以执行许多常用命令来查看、操作数据库和集合。以下是一些最常用的命令，分为几大类说明：

#### 🗂️ 数据库相关命令

| 命令                | 说明                                                     |
| ------------------- | -------------------------------------------------------- |
| `show dbs`          | 显示所有数据库列表                                       |
| `use mydb`          | 切换到名为 `mydb` 的数据库，如果不存在会在插入数据后创建 |
| `db`                | 显示当前使用的数据库                                     |
| `db.dropDatabase()` | 删除当前数据库（慎用）                                   |

#### 📁 集合（表）相关命令

| 命令                           | 说明                        |
| ------------------------------ | --------------------------- |
| `show collections`             | 显示当前数据库下的所有集合  |
| `db.createCollection("users")` | 创建一个名为 `users` 的集合 |
| `db.users.drop()`              | 删除 `users` 集合           |

#### 🧾 文档（数据）操作命令

##### 插入文档

```js
db.users.insertOne({ name: "Alice", age: 30 });
db.users.insertMany([{ name: "Bob" }, { name: "Charlie" }]);
```

##### 查询文档

```js
db.users.find(); // 查询全部
db.users.find({ age: 30 }); // 条件查询
db.users.findOne({ name: "Alice" }); // 查询一条
```

##### 更新文档

```js
db.users.updateOne({ name: "Alice" }, { $set: { age: 31 } });
db.users.updateMany({ age: { $lt: 25 } }, { $inc: { age: 1 } });
```

##### 删除文档

```js
db.users.deleteOne({ name: "Bob" });
db.users.deleteMany({ age: { $gt: 50 } });
```

#### 🔎 查询增强

```js
db.users.find().sort({ age: -1 }).limit(5); // 降序排序，取前5条
db.users.find().count(); // 查询总数
db.users.find({}, { name: 1, _id: 0 }); // 只显示 name 字段
```

#### 🛠️ 索引操作

```js
db.users.createIndex({ name: 1 });
db.users.getIndexes();
db.users.dropIndex({ name: 1 });
```

#### 📌 其他实用命令

| 命令                                          | 说明        |
| ------------------------------------------- | --------- |
| `db.stats()`                                | 显示数据库统计信息 |
| `db.users.stats()`                          | 显示集合统计信息  |
| `db.users.find().explain("executionStats")` | 查询优化分析    |
| `help`                                      | 查看命令帮助    |
| `db.help()`                                 | 数据库相关命令帮助 |
| `db.users.help()`                           | 集合方法帮助    |

如果想要查看文档结构（类似表结构）：

```js
db.users.findOne();
```

这个命令可以快速了解集合中有哪些字段。

### 举例说明

假设想要往一个名为 db1 的数据库里的名为 table1 的数据表里添加一条记录。如果是 MySQL 的话，先要创建 db1，然后创建 table1，最后追加记录。但是 MongoDB 却无需显式创建 db1 和 table1，步骤如下：

- `mongosh` 登录
- `use db1` 无论 db1 存在与否，都可以切换为当前数据库
- `db.table1.insertOne({})` ✅ 无论 table1 存在与否，都可以添加记录

常见错误如下（应该始终使用 `db` 这个变量获取当前数据库）

```shell
test> use db1
switched to db db1
db1> db1.table1.insertOne({}) ❌
ReferenceError: db1 is not defined
```

以下为完整终端的完整输出

```shell
Last login: Fri Jul 18 09:39:33 on ttys000
~ > mongosh
Current Mongosh Log ID:	6879a63d58b5bfdfbd0c16bb
Connecting to:		mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.5.5
Using MongoDB:		8.0.10
Using Mongosh:		2.5.5

For mongosh info see: https://www.mongodb.com/docs/mongodb-shell/

------
   The server generated these startup warnings when booting
   2025-07-18T06:55:33.121+08:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
------

test> show databases
admin    40.00 KiB
config  108.00 KiB
local    72.00 KiB
test>

test> use db1
switched to db db1
db1> db.table1.insertOne()
MongoshInvalidInputError: [COMMON-10001] Missing required argument at position 0 (Collection.insertOne)
db1> db.table1.insertOne({})
{
  acknowledged: true,
  insertedId: ObjectId('6879a69858b5bfdfbd0c16bc')
}
db1>
```

使用 MongoDB Compass 打开后，截图如下。可以看到 db1 下的 table1 中已经成功追加了一条记录。
![MongoDB_Compass](https://lib.zhaiduting.work.gd/uPic/MongoDB_Compass.png)
