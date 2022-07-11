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