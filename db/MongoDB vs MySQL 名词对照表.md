| MongoDB 概念         | MySQL 对应概念      | 说明                                    |
| ------------------ | --------------- | ------------------------------------- |
| **Database（数据库）**  | 数据库（Database）   | 相同概念，都是用于逻辑分组多个集合（表）                  |
| **Collection（集合）** | 表（Table）        | 类似一张表，用来存储结构相近的数据                     |
| **Document（文档）**   | 记录（Row）         | 类似表中的一条记录，但以 JSON 格式存储，更灵活            |
| **Field（字段）**      | 列（Column）       | 文档中的键（Key），对应表中的列名                    |
| **ObjectId**       | 主键（Primary Key） | MongoDB 默认每个文档都有一个 `_id`，类似 MySQL 的主键 |

## 🧾 举个例子更直观

### ✅ MySQL 表结构和记录：

```sql
CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(100),
  age INT
);

INSERT INTO users (name, age) VALUES ('Alice', 30);
```

数据看起来像这样：

| id  | name  | age |
| --- | ----- | --- |
| 1   | Alice | 30  |

### ✅ MongoDB 中的集合和文档：

```js
db.users.insertOne({ name: "Alice", age: 30 });
```

数据看起来像这样：

```json
{
	"_id": ObjectId("64f7..."),
	"name": "Alice",
	"age": 30
}
```

可以看到：

- `users` 是集合名，对应 MySQL 的表
- 每条记录是一个文档（Document），用 JSON 表示
- MongoDB 自动生成 `_id` 作为主键

## 📌 MongoDB 更灵活的一点

- **文档结构可以不一致**：  
     MongoDB 的集合中的每个文档不需要具有完全相同的字段，而 MySQL 中每行必须符合表结构。

例如，下面两个文档可以共存于同一个集合：

```js
{ name: "Alice", age: 30 }
{ name: "Bob", hobbies: ["reading", "gaming"] }
```

这在传统关系型数据库中是不允许的。

## ✅ 总结一句话：

> 集合 ≈ 表，文档 ≈ 表中的记录，一条文档 ≈ 一条记录

这个类比非常有助于从关系型数据库迁移到 MongoDB 思维模式。
