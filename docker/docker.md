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

##### 启动redis

```shell
docker run -d --network cc-net --restart always -v ~/config/redis/conf/redis.conf:/etc/redis/redis.conf -v ~/data/redis:/data --name redis -p 6379:6379  redis  --appendonly yes --requirepass "123456" 
```

##### 启动mysql

```
docker run -d --network cc-net --name mysql -p 3306:3306  -e MYSQL_ROOT_PASSWORD=123456 -v ~/config/mysql/conf:/etc/mysql/conf.d -v ~/data/mysql:/var/lib/mysql -e TZ=Asia/Shanghai --restart=always mysql
```

##### 启动pgsql

```
docker run --network cc-net --name postgresql --restart=always -e POSTGRES_USER=root -e POSTGRES_PASSWORD=123456 -p 5432:5432 -v ~/data/postgresql:/var/lib/postgresql/data -d postgres
```

##### 启动nacos

```shell
# mysql8对应镜像版本nacos/nacos-server:v2.2.2 空间不存在的话需要创建
docker run -d --name nacos -p 8848:8848 --network cc-net --link mysql:mysql -e MODE=standalone -e SPRING_DATASOURCE_PLATFORM=mysql -e MYSQL_SERVICE_HOST=192.168.5.23 -e MYSQL_SERVICE_PORT=3306 -e MYSQL_SERVICE_DB_NAME=nacos -e MYSQL_SERVICE_USER=root -e MYSQL_SERVICE_PASSWORD=123456 nacos/nacos-server:v2.2.2
```

##### 按条件批量删除容器

```
docker container ps -a | grep kube-* | awk '{print $1}' | xargs docker container rm --force
```

##### 网络空间

```shell
docker network ls 
#删除
docker network rm
#查看
docker network inspect 
#创建
docker network create app_net
```

##### 清理镜像

```shell
docker system prune
```

