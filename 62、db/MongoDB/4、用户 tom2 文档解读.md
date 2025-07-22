存储于 `admin.system.users` 集合中的 tom2 文档如下

```js
  {
    _id: 'test.tom2',
    userId: UUID('9f55f913-7bcf-4234-9653-8e88dc68a379'),
    user: 'tom2',
    db: 'test',
    credentials: {
      'SCRAM-SHA-1': {
        iterationCount: 10000,
        salt: 'TeD3RfbjZ18MM3Ml82OQAA==',
        storedKey: 'oQySFlMn+igaBRuZDwcrsKPuaGE=',
        serverKey: '77XAS2Jh9caBjNWdKc6WlBve4dg='
      },
      'SCRAM-SHA-256': {
        iterationCount: 15000,
        salt: 'qGesvvcySi5FPkdaPN5zC+HdSx484+grvWxJVQ==',
        storedKey: 'COzk0V2rdwBDuLQpz6FPkAVQe0eOfmwVwnnBaqvfstQ=',
        serverKey: 'Nw08NoH30McZDqUsJW2drcxMd+hH2iftg2DU2oUhHog='
      }
    },
    roles: [ { role: 'readWrite', db: 'test2' } ]
  }
```

### 一、关键字段的含义

#### ✅ `user: 'tom2'`

用户名，即登录 MongoDB 时使用的用户名。

#### ✅ `db: 'test'`

这代表：

- `tom2` **是在 `test` 数据库中创建的用户**；
- 登录时必须使用 `--authenticationDatabase test`，否则会出现认证失败。

```shell
mongosh -u tom2 -p 123 --authenticationDatabase test # ✅
# mongosh -u tom2 -p 123 --authenticationDatabase test2 # ❌
```

这是**身份验证所需的数据库名**，与权限无关。

#### ✅ `roles: [ { role: 'readWrite', db: 'test2' } ]`

这表示：

- `tom2` 拥有在 `test2` 数据库上的 `readWrite` 权限；
- 可以在 `test2` 中执行读写操作，例如：

```js
use test2
db.coll.insertOne({...})
db.coll.find()
```

但在其他数据库（包括 `test` 和 `admin`）中，没有任何权限。

### 二、`db: 'test'` 与 `roles[*].db: 'test2'` 的区别

| 字段                   | 含义                                                                                             |
| ---------------------- | ------------------------------------------------------------------------------------------------ |
| `db: 'test'`           | 用户属于哪个数据库（即在哪个库中创建的） <br>用于身份认证时指定认证库 `--authenticationDatabase` |
| `roles[*].db: 'test2'` | 指定该用户在哪个数据库具有什么权限（如 `readWrite`）                                             |

它们的**作用完全不同**：

| 用途           | `db: 'test'` | `roles[*].db`         |
| -------------- | ------------ | --------------------- |
| 登录认证       | ✅ 必须匹配  | ❌ 无关               |
| 权限判断       | ❌ 不相关    | ✅ 决定是否有读写权限 |
| 影响能不能插入 | ❌ 不影响    | ✅ 例如 `test2` 可写  |

### 三、举例说明

登录后在 `test2` 数据库能写入，而在 `test` 中不能，是因为：

- 登录认证基于 `db: 'test'`：你是 test 数据库中注册的用户；
- 授权仅限 `test2`：你仅被授予了 `readWrite@test2`；
- 所以在 `test` 里 `insertOne()` 会报权限错误。

### 四、推荐命令辅助查看

登录后可使用：

```
db.runCommand({ connectionStatus: 1 })
```

可以看到当前身份信息，例如：

```js
{
  authInfo: {
    authenticatedUsers: [ { user: 'tom2', db: 'test' } ],
    authenticatedUserRoles: [ { role: 'readWrite', db: 'test2' } ]
  },
  ok: 1
}
```

这再次明确了：

- 身份认证基于 `test`；
- 权限只在 `test2`。

如果你还想让 `tom2` 拥有 `test` 数据库的权限，可以这么做（用有权限的管理员用户）：

```js
use test
db.grantRolesToUser("tom2", [ { role: "readWrite", db: "test" } ])
```

### 五、终端命令分析

#### 可读写数据库 vs 认证数据库

authenticationDatabase 选项指定的是认证数据库（也就是创建此用户时使用的数据库）。搞错的话就会报错：Authentication failed.

| 项目           | 意义                               | 指定方式                   |
| -------------- | ---------------------------------- | -------------------------- |
| 认证数据库     | 用户在哪个库被创建（验证身份用）   | `--authenticationDatabase` |
| 可操作的数据库 | 用户在哪些库拥有权限（授权访问用） | `roles` 中的 `db` 字段决定 |

终端命令输出信息如下

```shell
~ > mongosh -u tom2 -p 123 --authenticationDatabase test2
Current Mongosh Log ID:	687f1f052ba1bf231eac039a
Connecting to:		mongodb://<credentials>@127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&authSource=test2&appName=mongosh+2.5.5
MongoServerError: Authentication failed.
~ >
~ > mongosh -u tom2 -p 123 --authenticationDatabase test
Current Mongosh Log ID:	687f1f3cd1a607127e9a7b4f
Connecting to:		mongodb://<credentials>@127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&authSource=test&appName=mongosh+2.5.5
Using MongoDB:		8.0.10
Using Mongosh:		2.5.5
mongosh 2.5.6 is available for download: https://www.mongodb.com/try/download/shell

For mongosh info see: https://www.mongodb.com/docs/mongodb-shell/

test>
```

#### 认证状态查询

使用 `db.runCommand({ connectionStatus: 1 })` 查询当前连接的认证状态

| 字段名                   | 含义                                           |
| ------------------------ | ---------------------------------------------- |
| `authenticatedUsers`     | 当前连接使用的用户及其认证数据库（认证身份用） |
| `authenticatedUserRoles` | 当前用户拥有的角色及其作用数据库（权限控制用） |
| `ok`                     | 值为 `1` 表示命令执行成功                      |

终端输出如下

```shell
test> db.runCommand({ connectionStatus: 1 })
{
  authInfo: {
    authenticatedUsers: [ { user: 'tom2', db: 'test' } ],
    authenticatedUserRoles: [ { role: 'readWrite', db: 'test2' } ]
  },
  ok: 1
}
```

#### 权限分析

登录成功后位于 `test>` 提示符下，插入数据失败，表明无权操作。切换到 test2 数据库后执行同样的命令，操作成功。以下为终端输出

```shell
test> db.ttt.insertOne({})
MongoServerError[Unauthorized]: not authorized on test to execute command { insert: "ttt", documents: [ { _id: ObjectId('687f13ec3b7dec94ef48c777') } ], ordered: true, lsid: { id: UUID("9c61bccc-1e0b-4c1d-b7b7-bdf5066b1d1e") }, $db: "test" }
test>
test> use test2
switched to db test2
test2> db.ttt.insertOne({})
{
  acknowledged: true,
  insertedId: ObjectId('687f14163b7dec94ef48c778')
}
```
