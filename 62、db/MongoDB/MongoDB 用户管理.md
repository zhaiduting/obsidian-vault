在 MongoDB Compass (v1.46.5) 中，并不存在用户管理的图形界面。目前只能通过 mongosh 以命令行的方式执行相关操作。

### 用户查询

如果想查询当前数据库下定义的用户，使用以下命令：

```shell
db.getUsers();
```

如果想查询全系统用户，并且当前数据库不是 `admin` 数据库，使用以下命令：

```shell
use admin # 必须切换数据库
db.system.users.find()
```

### 用户登录

假设 test 用户存在以下用户

```shell
test> db.getUsers();
{
  users: [
    {
      _id: 'test.tom',
      userId: UUID('36b275f8-35c9-417b-af17-ce33fe735830'),
      user: 'tom',
      db: 'test',
      roles: [ { role: 'readWrite', db: 'test' } ],
      mechanisms: [ 'SCRAM-SHA-1', 'SCRAM-SHA-256' ]
    },
    {
      _id: 'test.tom2',
      userId: UUID('9f55f913-7bcf-4234-9653-8e88dc68a379'),
      user: 'tom2',
      db: 'test',
      roles: [ { role: 'readWrite', db: 'test2' } ],
      mechanisms: [ 'SCRAM-SHA-1', 'SCRAM-SHA-256' ]
    }
  ],
  ok: 1
}
```

以下两条命令都可以成功登录

```shell
mongosh -u tom -p 123 --authenticationDatabase test # ✅
mongosh -u tom2 -p 123 --authenticationDatabase test # ✅
# mongosh -u tom2 -p 123 --authenticationDatabase test2 # ❌
```
