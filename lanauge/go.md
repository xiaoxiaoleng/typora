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
go env
go mod init  # 初始化go.mod
go mod tidy  # 更新依赖文件
go mod download  # 下载依赖文件
go mod vendor  # 将依赖转移至本地的vendor文件
go mod edit  # 手动修改依赖文件
go mod graph  # 打印依赖图
go mod verify  # 校验依赖
#
https://github.com/goproxy/goproxy.cn/issues/29
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



## 函数用法

| 函数用法                                                     | 描述                                     |
| :----------------------------------------------------------- | :--------------------------------------- |
| [函数作为另外一个函数的实参](https://www.runoob.com/go/go-function-as-values.html) | 函数定义后可作为另外一个函数的实参数传入 |
| [闭包](https://www.runoob.com/go/go-function-closures.html)  | 闭包是匿名函数，可在动态编程中使用       |
| [方法](https://www.runoob.com/go/go-method.html)             | 方法就是一个包含了接受者的函数           |

```
Go 语言支持匿名函数，可作为闭包。匿名函数是一个"内联"语句或表达式。匿名函数的优越性在于可以直接使用函数内的变量，不必申明。
以下实例中，我们创建了函数 getSequence() ，返回另外一个函数。该函数的目的是在闭包中递增 i 变量，代码如下：
```

```go
package main

import "fmt"

func getSequence() func() int {
   i:=0
   return func() int {
      i+=1
     return i  
   }
}

func main(){
   /* nextNumber 为一个函数，函数 i 为 0 */
   nextNumber := getSequence()  

   /* 调用 nextNumber 函数，i 变量自增 1 并返回 */
   fmt.Println(nextNumber())
   fmt.Println(nextNumber())
   fmt.Println(nextNumber())
   
   /* 创建新的函数 nextNumber1，并查看结果 */
   nextNumber1 := getSequence()  
   fmt.Println(nextNumber1())
   fmt.Println(nextNumber1())
}
```

##### Go 语言函数方法

```
Go 语言中同时有函数和方法。一个方法就是一个包含了接受者的函数，接受者可以是命名类型或者结构体类型的一个值或者是一个指针。所有给定类型的方法属于该类型的方法集。语法格式如下：
```

```go
package main

import (
   "fmt"  
)

/* 定义结构体 */
type Circle struct {
  radius float64
}

func main() {
  var c1 Circle
  c1.radius = 10.00
  fmt.Println("圆的面积 = ", c1.getArea())
}

//该 method 属于 Circle 类型对象中的方法
func (c Circle) getArea() float64 {
  //c.radius 即为 Circle 类型对象中的属性
  return 3.14 * c.radius * c.radius
}
```

##### 脚手架go-web

https://developer.aliyun.com/article/1180078