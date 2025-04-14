sql.DB.Prepare()  方法会返回一個  sql.Stmt  对象，与 Stmt 相关的方法如下：
stmt.Exec()
stmt.Query()
stmt.QueryRow()
stmt.Close()

sql.DB 也有对应的如下方法：
sql.DB.Exec()
sql.DB.Query()
sql.DB.QueryRow()
sql.DB.Close()

这些对应方法有什么区别呢？

### Exec() 方法对比

```go
// 使用DB直接执行(适合一次性操作)
db.Exec("INSERT INTO users(name, age) VALUES(?, ?)", "Alice", 30)


// 使用Stmt执行(适合重复操作)
stmt, err := db.Prepare("INSERT INTO users(name, age) VALUES(?, ?)")
if err != nil {
    log.Fatal(err)
}
defer stmt.Close()

for _, user := range users {
    stmt.Exec(user.Name, user.Age)
}
```

### QueryRow() 方法对比

为简化代码，以下两例的错误判断代码省略了 err 等于 sql.ErrNoRows 时的处理逻辑。如果不省略的话，错误处理类似于下面这样

```go
if err != nil {
    if err == sql.ErrNoRows {
        fmt.Println("没有找到该用户")
    } else {
        log.Fatal(err)
    }
} else {
    fmt.Println(...)
}
```

#### 使用 DB

```go
// 查询单个用户年龄
var age int
err := db.QueryRow("SELECT age FROM users WHERE name = ?", "Alice").Scan(&age)
if err != nil {
    log.Fatal(err)
}
fmt.Println("Alice的年龄:", age)
```

#### 使用 Stmt 执行

```go
// 准备查询语句
stmt, err := db.Prepare("SELECT age FROM users WHERE name = ?")
if err != nil {
    log.Fatal(err)
}
defer stmt.Close()

// 查询多个用户的年龄
for _, name := range []string{"Alice", "Bob"} {
    var age int
    err := stmt.QueryRow(name).Scan(&age)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("%s的年龄: %d\n", name, age)
}
```

### Query() 方法对比

Query 通常会涉及到循环处理多条记录，用法要比 QueryRow 复杂。注意：QueryRow 需要考虑 err 等于 sql.ErrNoRows 时的处理逻辑，而 Query 却不需要。因为 Query() 返回的  Rows  对象可以通过 rows.Next() 自然处理空结果。

以下为 Query() 简化模式

```go
rows, err := db.Query(...)
if err != nil { /* 只处理非空错误 */ }
defer rows.Close()
for rows.Next() { /* 自动处理空结果集 */ }
// 忽略 rows.Err() 相关逻辑
```

标准模式应该包含 rows.Err() 检查，如下所示

```go
rows, err := db.Query("SELECT ...")
if err != nil {
    return fmt.Errorf("查询失败: %w", err) // 或 log.Fatal(err)
}
defer rows.Close() // 必须关闭

for rows.Next() {
    var data YourStruct
    if err := rows.Scan(&data); err != nil {
        return fmt.Errorf("扫描行失败: %w", err)
    }
    // 处理数据...
}

// 关键检查 ↓
if err := rows.Err(); err != nil {
    return fmt.Errorf("遍历结果集时出错: %w", err)
}
```

#### 使用 DB

```go
// 简单查询所有用户名字
rows, err := db.Query("SELECT name FROM users")
if err != nil {
    log.Fatal(err)
}
defer rows.Close() // 记得关闭

var names []string
for rows.Next() {
    var name string
    if err := rows.Scan(&name); err != nil {
        log.Fatal(err)
    }
    names = append(names, name)
}
fmt.Println("所有用户:", names)
```

#### 使用 Stmt 执行

```go
// 准备查询语句
stmt, err := db.Prepare("SELECT name FROM users WHERE age > ?")
if err != nil {
    log.Fatal(err)
}
defer stmt.Close()

// 查询不同年龄段的用户
for _, age := range []int{18, 30} {
    rows, err := stmt.Query(age)
    if err != nil {
        log.Fatal(err)
    }

    var names []string
    for rows.Next() {
        var name string
        if err := rows.Scan(&name); err != nil {
            log.Fatal(err)
        }
        names = append(names, name)
    }
    rows.Close()

    fmt.Printf("年龄>%d的用户: %v\n", age, names)
}
```
