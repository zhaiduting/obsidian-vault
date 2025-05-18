这东西类似 php 中的 var_dump()，而且功能更强大。详细说明可查看[官方文档](https://pkg.go.dev/github.com/davecgh/go-spew/spew)

### 安装方法

```shell
go get -u github.com/davecgh/go-spew/spew
```

如果之前安装过测试工具 testify 则无需敲入上述命令，因为 testity 的依赖包中已经安装了 spew

### 基本用法

```go
import "github.com/davecgh/go-spew/spew"
var DB = ...
func main() {
    spew.Dump(DB) // DB 为
}
```

但是这样输出的结果太吓人，多达 1094 行。数据结构因为过于复杂反而不利于阅读，于是需要使用 MaxDepth 限制深层迭代的次数，如下所示

```go
spew.Config.MaxDepth = 2
spew.Dump(DB)
```

输出结果缩小到 64 行，相对于之前的一千多行要清爽得多

```
(*sql.DB)(0x1400010fc70)({
 waitDuration: (atomic.Int64) {
  _: (atomic.noCopy) {
   <max depth reached>
  },
  _: (atomic.align64) {
   <max depth reached>
  },
  v: (int64) 0
 },
 connector: (*mysql.connector)(0x1400000c1c8)({
  cfg: (*mysql.Config)(0x14000000900)({
   <max depth reached>
  }),
  encodedAttributes: (string) (len=89) "\f_client_name\x0fGo-MySQL-Driver\x03_os\x06darwin\t_platform\x05arm64\x04_pid\x044295\f_server_host\t127.0.0.1"
 }),
 numClosed: (atomic.Uint64) {
  _: (atomic.noCopy) {
   <max depth reached>
  },
  _: (atomic.align64) {
   <max depth reached>
  },
  v: (uint64) 0
 },
 mu: (sync.Mutex) {
  _: (sync.noCopy) {
   <max depth reached>
  },
  mu: (sync.Mutex) {
   <max depth reached>
  }
 },
 freeConn: ([]*sql.driverConn) (len=1 cap=1) {
  (*sql.driverConn)(0x14000134a00)({
   <max depth reached>
  })
 },
 connRequests: (sql.connRequestSet) {
  s: ([]sql.connRequestAndIndex) <nil>
 },
 numOpen: (int) 1,
 openerCh: (chan struct {}) (cap=1000000) 0x1400007c540,
 closed: (bool) false,
 dep: (map[sql.finalCloser]sql.depSet) (len=1) {
  (*sql.driverConn)(0x14000134a00)({
   <max depth reached>
  }): (sql.depSet) (len=1) {
   <max depth reached>
  }
 },
 lastPut: (map[*sql.driverConn]string) {
 },
 maxIdleCount: (int) 0,
 maxOpen: (int) 0,
 maxLifetime: (time.Duration) 0s,
 maxIdleTime: (time.Duration) 0s,
 cleanerCh: (chan struct {}) <nil>,
 waitCount: (int64) 0,
 maxIdleClosed: (int64) 0,
 maxIdleTimeClosed: (int64) 0,
 maxLifetimeClosed: (int64) 0,
 stop: (func()) 0x100aa9f00
})
```

或者改用如下写法也是可以的

```go
cfg := spew.ConfigState{
    Indent: "  ",  // 缩进2空格，默认缩进只有1空格
    MaxDepth: 2,
}
cfg.Dump(DB)
```

或者改用如下链式写法

```go
// 方法接收者为字面量时，不会自动取址
// 因此，下面的取址符号 & 不能省略
(&spew.ConfigState{  
    Indent:   "\t",  
    MaxDepth: 2,  
}).Dump(DB)
```
