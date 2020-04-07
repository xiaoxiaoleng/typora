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

