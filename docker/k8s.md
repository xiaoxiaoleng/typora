##### 查看有哪些服务实例

```shell
kubectl get pod
# 列出所有命名空间下的全部的 Pods
kubectl get pods --all-namespaces
```

##### 进入服务实例

```shell
#进入pod
kubectl exec -ti <your-pod-name>  -n <your-namespace>  -- /bin/sh
#example:
kubectl exec -ti isc-flow-service-548d6cff76-9xmbl -- /bin/sh
```

##### 进入docker容器

```shell
docker exec -ti  <your-container-name>   /bin/sh
```

##### 编辑副本数

```shell
#直接编辑文件
kubectl edit deploy isc-flow-service
...
#使用命令直接修改副本数量
kubectl scale deploy/isc-dynomicform-service --replicas=2
```

##### 查看升级信息

```shell
#查看升级信息
kubectl describe deployment familytree-api
```

##### 外部文件复制到容器内

```shell
kubectl -n namespace cp ./file.yaml pod-hash:/folder/file.yaml
#将宿主机 /tmp/test_pod.txt 拷贝到 mycentos-7b59b5b755-8rbgc 对应的第一个容器中的 /root 目录下，拷贝#目录也是一样的。default 为 namespace
kubectl cp /tmp/test_pod.txt default/mycentos-7b59b5b755-8rbgc:/root
```

##### copy容器内文件到主机

```shell
#将 mycentos-7b59b5b755-8rbgc 中 /root/from_pod.txt 文件拷贝到宿主机 /tmp 目录下，并命名为 #from_pod.new
kubectl cp default/mycentos-7b59b5b755-8rbgc:/root/pod_dir /tmp
#kubectl cp default/isc-common-service-bffb846d9-8z6db:/home/isc-common-service/heap.hprof /home
#tar: removing leading '/' from member names
#I moved the wanted file to the working dir in the pod (the directory which is automatically #opened, when you open bash on it)
kubectl cp default/isc-common-service-bffb846d9-8z6db:heap.hprof /home/heap.hprof
```

##### 服务重启

```shell
#在有 yaml 文件的情况下可以直接使用
kubectl replace --force -f xxxx.yaml 
#没有 yaml 文件，但是使用的是 Deployment 对象
kubectl scale deployment esb-admin --replicas=0 -n {namespace}
kubectl scale deployment esb-admin --replicas=1 -n {namespace}
#由于 Deployment 对象并不是直接操控的 Pod 对象，而是操控的 ReplicaSet 对象，而 ReplicaSet 对象就是由副本的数目的定义和Pod 模板组成的。所以这条命令分别是将ReplicaSet 的数量 scale 到 0，然后又 scale 到 1，那么 Pod 也就重启了。

#没有 yaml 文件，直接使用 Pod 对象重启
kubectl get pod {podname} -n {namespace} -o yaml | kubectl replace --force -f -
```

##### 查看一下相关的日志

```shell
kubectl describe pods test-pod
# describe 命令的详细输出
kubectl describe nodes my-node
kubectl describe pods my-pod
```

##### 编辑配置

```shell
kubectl edit deploy isc-ias-service
```

