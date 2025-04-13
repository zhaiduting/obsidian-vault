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

### Exec()

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
