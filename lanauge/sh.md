# shell 基础语法

##### 启动本地测试端口

```
nc -lk 8080
```

#### 查看ip

```
ip addr
```



### 常见问题

###### 1）ssh command not found

```shell
#checked the sshd service as below
service sshd status
```



##### 查看僵死进程

```shell
ps -al | gawk '{print $2,$4}' | grep Z
```

##### 内存使用百分比

```shell
free | sed -n '2p' | gawk 'x = int(( $3 / $2 ) * 100) {print x}' | sed 's/$/%/'
```

##### 磁盘使用百分比

```shell
df -h /dev/sda1 | sed -n '/% \//p' | gawk '{ print $5 }'
```

##### 查看大于800M文件

```
find . -type f -size +800M
```

##### 查看某一进程使用情况

```
top -p 2913
```

##### 内存占用前十的进程

```
ps aux | sort -k4,4nr | head -n 10
```

##### 根据进程查端口号

```shell
netstat -ltp | grep 7578
```

##### 根据端口查进程

```
ss -anp|grep 7578
ss -pl | grep 3306
```

##### 测试端口

```
telnet 10.1.62.161 7578

nc -v 10.1.62.161 7578
nc -zv 10.1.62.161 7578

nmap 10.1.62.161 -p 7577
```

##### 防火墙

```
* 永久打开一个端口：
firewall-cmd --permanent --add-port=8080/tcp
* 永久关闭一个端口：
firewall-cmd --permanent --remove-port=8080/tcp
* 永久打开某项服务：
firewall-cmd --permanent --add-service=http
* 永久关闭某项服务：
firewall-cmd --permanent --remove-service=http
* 进行端口转发：
firewall-cmd --permanent --add-forward-port=port=80:proto=tcp:toport=8080:toaddr=192.0.2.55
* 允许转发到其他地址：
firewall-cmd --permanent --add-masquerade
* 重新加载防火墙：
firewall-cmd --reload

* 启动：
systemctl start firewalld
* 查看状态：
systemctl status firewalld 或者 firewall-cmd –state
* 停止：
systemctl disable firewalld
* 禁用：
systemctl stop firewalld

查询端口号80 是否开启：
firewall-cmd --query-port=80/tcp
永久开放80端口号：
firewall-cmd --permanent --zone=public --add-port=80/tcp
移除80端口号：
firewall-cmd --permanent --zone=public --remove-port=80/tcp
--zone 作用域
--add-port=80/tcp 
添加端口，格式为：端口/通讯协议
--permanent   #永久生效，没有此参数重启后失效
查看防火墙状态
systemctl status firewalld.service
启动|关闭|重新启动  防火墙
systemctl [start|stop|restart] firewalld.service 
```



##### curl post

```
curl -X POST --header 'Content-Type: multipart/form-data' --header 'Accept: application/json' --header 'apikey: e10adc3949ba59abbe56e057f2gg88dd' -F 'dir=root' -F 'filename=test.txt' -F 'file=@test.txt' 'http://128.96.104.181/store/fs/openapi/v2/root/test.txt'
```

##### 获取文件大小

```shell
#列出当前目录下所有文件的大小
ls -lht 
#或者
ll
#使用du -sh * 命令也可以列出当前文件以及文件夹的大小。
du -sh *
#统计当前文件夹(目录)大小，并按文件大小排序
du -sh * | sort -n
```

##### 排序

```shell
#降序：
ls -lrsh
#升序:
ls -lsh
```



##### 拷贝文件

```
拷贝文件到172.19.2.75:/home/root
scp -r /home/daisy/full.tar.gz root@172.19.2.75:/home/root

scp /Users/xiaozhong/mysql/init/TestMysql.java root@10.1.241.56:/opt/platform-mysql/

scp -r /Users/xiaozhong/OS broada@10.1.241.23:~

scp /Users/xiaozhong/mysql/init/TestMysql.java root@10.1.241.56:
```

##### 登录ftp服务器

```
sftp -P custom_port remote_username@server_ip_or_hostname
sftp -P 22222 2022030403781335@sftp.kftpay.com.cn
```

##### 传送文件

```
#下载文件
sftp> get filename.zip local_filename.zip
#上传文件
sftp> put -r /home/20220318142520001.zip
```

##### 找到命令对应的原文件位置

```shell
which java
ls -l /usr/bin/java
ls -al /etc/alternatives/java
```

##### mac刷新DNS信息

```shell
sudo killall -HUP mDNSResponder
```

##### 时间范围内的查找

```shell
sed -n '/11:07 18:29:20/,/11:07 18:31:11/p' catalina.out 
sed -n '/11:07 18:29:/,/11:07 18:31:/p' catalina.out
```

##### Shell在线调试

```shell
sh -x ./h2_master.sh restart
```

##### 根据端口获取进程PID

```shell
ps -ef | grep  9101  | grep -v grep | awk '{print $2}')"
```

##### 定时输出指标状态信息

```shell
#每个一秒输出jvm信息
watch -n 1 jstat  -gcutil 20010
```

##### 定位日志

```shell
#得到关键日志的行号
cat -n test.log | grep “关键词” 
#表示查询13230539行之后的日志
tail -n +13230539

```

##### 配置hosts文件

```
echo "127.0.0.1 dev.appsmith.com" | sudo tee -a /etc/hosts
```

##### sdk 版本管理

```shell
sdk current
sdk list java
sdk upgrade
sdk install java 11.0.19-tem
sdk use java 11.0.19-tem
sdk default java 11.0.19-tem
sdk uninstall java 14.0.1.j9-adpt
sdk flush
```

##### 分割查看

+ 分割大文件  split -l 100000 baafcb066e92940a30425d61ade7083659764decc377ffc2242b6e90012c37fd-json.log cchub_log_part_
+ 查看特定时间点的日志 grep "2024-06-24 15:45" /var/log/myapp.log
+ 查看特定时间点及前后10行日志 grep -A 10 -B 10 "2024-06-24 15:45" /var/log/myapp.log
+ 用less翻页查看特定时间点的日志 grep "2024-06-24 15:45" /var/log/myapp.log | less
+ 用awk查看特定时间点的日志 awk '$0 ~ /2024-06-24 15:45/' /var/log/myapp.log



###### 检索内容

```shell
find / -type f -name "*.*" | xargs grep "bootstrap.min.css.map"
```

