在 go 项目的 main 函数中拿到 gin 引擎，将其传递给 CmsRouter() 以创建路由，最后运行引擎

```go
func main() {
    r := gin.Default()
    api.CmsRouter(r)
    r.Run()
}
```

CmsRouter 创建路由的代码如下，对于每个具体的 url 请求，实际上是交给 CmsApp 实例的相应方法去处理的

```go
func CmsRouter(r *gin.Engine) {
    cmsApp := services.NewCmsApp()
    session := &SessionAuth{}
    root := r.Group("/api").Use(session.Auth)
    {
       root.GET("/ping", cmsApp.Hello)
    }
    out := r.Group("/out/api")
    {
       out.POST("/register", cmsApp.Register)
    }
}
```
在实例化CmsApp时，调用gorm.Open打开数据库，并将其绑定为CmsApp的db属性，这是