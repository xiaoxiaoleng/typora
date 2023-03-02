##### 进入docker容器

```shell
docker exec -ti  <your-container-name>   sh  | docker exec -it 0606ec23972a sh

docker exec -ti  <your-container-name>   /bin/sh
```

##### 查看容器服务

```
docker ps
docker ps|grep mongo
```

##### 将容器的ip192.168.0.100和80端口，映射到宿主机的8000端口

```
docker run -p 192.168.0.100:8000:80 -it ubuntu /bin/bash
#将nginx的80端口映射到宿主机的800端口上
docker run -d -it -p 800:80 nginx 
```

##### 查看映射端口配置

```
docker port container_ID #容器ID
#结果输出
80/tcp -> 0.0.0.0:800
```

##### 查看镜像哈希值

```
docker images --digests
```

##### 查询镜像信息(挂载)

```shell
docker inspect container_ID
```

##### 使用 `--mount` 标记可以指定挂载一个本地主机的目录到容器中去

```shell
docker run -d -P \
    --name web \
    # -v /src/webapp:/usr/share/nginx/html \
    --mount type=bind,source=/src/webapp,target=/usr/share/nginx/html \
    nginx:alpine
上面的命令加载主机的 /src/webapp 目录到容器的 /usr/share/nginx/html目录。这个功能在进行测试的时候十分方便，比如用户可以放置一些程序到本地目录中，来查看容器是否正常工作。本地目录的路径必须是绝对路径，以前使用 -v 参数时如果本地目录不存在 Docker 会自动为你创建一个文件夹，现在使用 --mount 参数时如果本地目录不存在，Docker 会报错。    
```

##### 保存镜像

```shell
docker save city-brain > ./city-brain.tar
```

##### 加载镜像

```
docker load --input  /city-brain.tar
```

