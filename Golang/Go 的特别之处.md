相对于 JS、PHP 及 C 等语言，Go 的某些语法非常特别

- 大写字母开头的可以导出，小写开头的不可以导出
- 包名不能包含路径
- internal package
- 结构体内部不能定义方法
- 继承的奇怪写法

文件 internal/auth/auth.go 内容

```go
package auth

// Authenticator 是一个接口，用于身份验证
type Authenticator interface {
    Authenticate(username, password string) (bool, error)
}
```

文件 internal/auth/internal.go 内容

```go
package auth

// internalAuthenticator 是 Authenticator 接口的实现
type internalAuthenticator struct{}

func (a *internalAuthenticator) Authenticate(username, password string) (bool, error) {
    // 这里是具体的身份验证逻辑
    return username == "admin" && password == "123456", nil
}

// NewAuthenticator 返回一个 Authenticator 接口的实现
func NewAuthenticator() Authenticator {
    return &internalAuthenticator{}
}
```

文件 pkg/api/api.go 内容如下

```go
package api

import (
    "authguard/internal/auth"
)

// Server 是一个 API 服务器，依赖 Authenticator 接口
type Server struct {
    auth.Authenticator
}

// Login 是 Server 的一个方法，用于处理登录
func (s *Server) Login(username, password string) bool {
    ok, err := s.Authenticate(username, password)
    if err != nil {
       return false
    }
    return ok
}

// NewServer 创建一个 Server 实例
func NewServer() *Server {
    return &Server{
       auth.NewAuthenticator(), // 调用 internal/auth 中的 NewAuthenticator    }
}
```
