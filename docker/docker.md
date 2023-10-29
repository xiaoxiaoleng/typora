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

```shell
#支持时序
docker run --network cc-net --name postgresql --restart=always -e POSTGRES_USER=root -e POSTGRES_PASSWORD=123456 -p 5432:5432 -v ~/data/postgresql:/var/lib/postgresql/data -d timescale/timescaledb-ha:pg14-latest

#安装最新版本
docker run -d --network cc-net --name postgresql --restart=always -e POSTGRES_USER=root -e POSTGRES_PASSWORD=123456 -p 5432:5432 -v ~/data/postgresql:/var/lib/postgresql/data postgres
```

##### 启动nacos

```shell
# mysql8对应镜像版本nacos/nacos-server:v2.2.2 空间不存在的话需要创建
docker run -d --name nacos -p 8848:8848 --network cc-net --link mysql:mysql -e MODE=standalone -e SPRING_DATASOURCE_PLATFORM=mysql -e MYSQL_SERVICE_HOST=127.0.0.1 -e MYSQL_SERVICE_PORT=3306 -e MYSQL_SERVICE_DB_NAME=nacos -e MYSQL_SERVICE_USER=root -e MYSQL_SERVICE_PASSWORD=123456 nacos/nacos-server:v2.2.2
```

##### 启动mongo

```shell
docker run -d --privileged --restart=unless-stopped --network cc-net --name mongo -p 27017:27017 -v ~/data/mongo:/data/db \
      -e MONGO_INITDB_ROOT_USERNAME=admin \
      -e MONGO_INITDB_ROOT_PASSWORD=123456 \
      mongo --auth
```

##### 运行superset

```shell
docker run -d --privileged --restart=unless-stopped --network cc-net -p 8088:8088 -e SUPERSET_SECRET_KEY=123456 --name superset apache/superset

docker exec -it superset superset fab create-admin \
              --username admin \
              --firstname Superset \
              --lastname Admin \
              --email admin@superset.com \
              --password admin

docker exec -it superset superset db upgrade

#可跳过不执行
docker exec -it superset superset load_examples
#初始化数据
docker exec -it superset superset init

#本地部署操作
https://github.com/apache/superset/tree/master/docker#readme

#部署本地的镜像
#默认内置数据库地址 /app/superset_home/superset.db
#mysql://mysqluser:mysqluserpassword@localhost/superset?charset=utf8
docker run -d --privileged --restart=unless-stopped --network cc-net -p 8088:8088 -e SUPERSET_SECRET_KEY=lWJNih/Aklg/m9PKGwpsmI6wMFPN3sbWQxP57JEMNYr99si6OLfSOrlU --name superset  superset_local

docker run -d --privileged --restart=unless-stopped --network cc-net -p 8088:8088 -e SUPERSET_SECRET_KEY=lWJNih/Aklg/m9PKGwpsmI6wMFPN3sbWQxP57JEMNYr99si6OLfSOrlU --name superset -v ~/data/superset/:/app/superset_home/  superset_local

docker cp superset:/app/superset_home/superset.db ~/data/superset
```

https://hub.docker.com/r/apache/superset



##### 启动本地nacos

````shell
docker-compose -f /Users/xiaozhong/docker/nacos/nacos-docker/example/docker-compose.yaml up -d
````



#### mqtt

```shell
docker run -d --restart unless-stopped --network cc-net --privileged --name mosquitto -p 1883:1883 \
 -v ~/docker/mqtt/config/mosquitto.conf:/mosquitto/config/mosquitto.conf \
 -v ~/docker/mqtt/data:/mosquitto/data \
 -v ~/docker/mqtt/log:/mosquitto/log \
 eclipse-mosquitto:1.6.14
```

##### 生成mqtt密钥文件

```shell
#在无密码状态下先启动创建密钥并将文件挂载到对应目录
touch /mosquitto/config/password.conf
chmod -R 755 /mosquitto/config/password.conf
mosquitto_passwd -b /mosquitto/config/password.txt root 123456
#配置文件
listener 1883
persistence true
allow_anonymous false
password_file /mosquitto/config/password.conf
#password_file ~/docker/mqtt/config/password.conf
```

##### 发送topic(在mosquitto容器内执行)

```shell
mosquitto_pub -h 127.0.0.1 -p 1883 -t mytopic -m "Hello, MQTT!" -u root -P 123456
```

##### 部署emqx

```shell
#http://localhost:18083/ 默认账户密码admin/public
docker run -d --name emqx --network cc-net --privileged --restart always -p 1883:1883 -p 8083:8083 -p 8084:8084 -p 8883:8883 -p 18083:18083 emqx/emqx:5.1.1
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

##### 将文件copy到容器内

```
docker cp output_file.dump postgresql:~
```

##### 容器内文件copy到宿主机

```
docker cp postgresql:/home/postgres/output_file.sql /Users/xiaozhong/Downloads
```

