jvm



```
-XX:MetaspaceSize=128M -XX:MaxMetaspaceSize=256M -Xms256m -Xmx256m
```

```
为什么MetaspaceSize要设置为128M？为什么堆内存初始值Xms设置为256M而不是512M？
```

##### jmap 手动生成dump文件

```
jmap -dump:format=b,file=heapdump.hprof 3331
```

##### JDK_HOME/bin目录下，可搜：jvisualvm 打开dump文件



##### jstatd的安装、启动、连接

```shell
1、在服务器上新建文件，命名为jstatd.all.policy内容为：
grant codebase "file:${java.home}/../lib/tools.jar" { 
   permission java.security.AllPermission; 
};
```

##### 注意

```
注：此处策略中的java.home，和JAVA_HOME不是一个概念，不要搞错了，此处的java.home指的是JRE的路径，这个是Java的系统属性，不需要手工指定，通常是这个jdk下面的jre路径,即可以认为${java.home}和${JAVA_HOME}/jre是等价，如果想查看这个变量的值，可以任意找一个运行着的Java应用，找到它的PID，然后通过如下jinfo命令查看就可以查看到java.home的值：
jinfo ${PID}|grep java.home
```



```
2、在远程主机上启动 jstatd 并且不要关闭。

在服务器上jstatd.all.policy所在目录下执行下面的命令
其中 /usr/local/java/bin/jstatd为jstatd所在路径，为${JAVA_HOME}/bin/jstatd
/usr/local/java/bin/jstatd -J-Djava.security.policy=jstatd.all.policy 
想指定端口可以用下面命令：
jstatd -J-Djava.security.policy=jstatd.policy -p 1099
```







