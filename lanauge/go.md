##### 官方学习地址:https://go.dev/learn/

#####中文地址:https://tour.go-zh.org/welcome/1

##### 查看go环境变量

```go
go env

go version
```

##### 输出格式化

```go
fmtgo main.go
fmtgo  -w main.go  //会将源码文件格式化
```

##### 获取依赖
```
go get
go get下载的依赖包会放到GOPATH 目录下，因此获取前需要确保 GOPATH 已经设置。Go 1.8 版本之后，GOPATH 默认在用户目录的 go 文件夹下。


我们需要将依赖下载至本地，但不使用 go get，而是使用以下命令
go mod download 
然而如果你没有设置 GOPROXY 为国内镜像,这步百分百会卡死。
设置镜像的语句（最好把他们写进 ~/.bashrc 中，不然每次打开Terminal都要执行一次）
export GO111MODULE=on
export GOPROXY=https://goproxy.io

```

##### 常用命令
```
go mod init  # 初始化go.mod
go mod tidy  # 更新依赖文件
go mod download  # 下载依赖文件
go mod vendor  # 将依赖转移至本地的vendor文件
go mod edit  # 手动修改依赖文件
go mod graph  # 打印依赖图
go mod verify  # 校验依赖
```

##### 变量声明(批量声明),使用关键字 var 和括号，可以将一组变量定义放在一起。
```
var (
    a int
    b string
    c []float32
    d func() bool
    e struct {
        x int
    }
)
```

##### 切片
- 1.只读取key：
```
for key := range oldMap 
```
- 2.只读取value:
```
for _, value := range oldMap
```

##### go并发（关键字来开启 goroutine ）
```
goroutine 是轻量级线程，goroutine 的调度是由 Golang 运行时进行管理的。
goroutine 语法格式：go 函数名( 参数列表 )
如: go f(x, y, z)
```

##### 通道 通道（channel）是用来传递数据的一个数据结构
- 通道可用于两个 goroutine 之间通过传递一个指定类型的值来同步运行和通讯。操作符 <- 用于指定通道的方向，发送或接收。如果未指定方向，则为双向通道。
```
ch <- v    // 把 v 发送到通道 ch
v := <-ch  // 从 ch 接收数据
           // 并把值赋给 v
```

##### 函数定义
```go
在声明函数参数和类型的时候，与声明变量类似，可以一次性指定多个参数的类型，也可以分别指定多个参数为不同类型。
func max (n1 int, n2 int) int {   &hellip;&hellip; }
如果函数返回值有多个，在指定返回类型的时候，需要指定每个返回值的类型。
func max (n1 int, n2 int) (error, int) {   &hellip;&hellip;   return errors.New(""), result }
```
> 在 go 中，有两个特殊函数：main、init，这两个函数声明之后，一般不需要主动调用，会有自动执行的机制。main  函数有如下几点需要注意：
不能定义参数;
不能定义返回值;
必须在 package main 中声明;
> init 函数所有的包启动的时候都会执行，执行时机比 main 函数早，与 main 函数一样，不能定义参数和返回值。

##### 匿名函数
```
匿名函数就是一个没有定义函数名的函数，匿名函数可以当成一个值，将其赋值放到某个变量中。这也是之前为什么说函数是『一等公民』，就是可以将函数当成一个变量。
```
> var max = func (n1, n2 int) int {  
	var result int   
if n1 >= n2 {     result = n1   }   
if n1 < n2 {     result = n2   }   
return result }
var result = max(5, 100)
fmt.Println("max return", result)