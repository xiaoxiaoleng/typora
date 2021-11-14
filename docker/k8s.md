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
#直接编辑文件（更新镜像）
kubectl edit deploy isc-flow-service
...
kubectl edit svc isc-flow-service
...
#使用命令直接修改副本数量
kubectl scale deploy/isc-dynomicform-service --replicas=2
```

##### 检查端口

```
kubectl get svc |grep 29011
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

#####Telepresence：让微服务本地开发不再难

```
Telepresence是一款为Kubernetes微服务框架提供快速本地化开发功能的开源软件。Telepresence在Kubernetes集群中运行的Pod中部署双向网络代理，该Pod将Kubernetes环境（如TCP连接，环境变量，卷）中的数据代理到本地进程。本地进程透明地覆盖其网络，以便DNS调用和TCP连接通过代理路由到远程Kubernetes集群，能够获取远端K8S集群的各项资源。该工具可以实现：

本地服务可以完全访问远程群集中的其他服务；
本地服务可以完全访问Kubernetes的环境变量，Secrets和ConfigMap；
K8S中运行的远程服务也可以完全访问本地服务
```

###### get 命令的基本输出

```
kubectl get services                          # 列出当前命名空间下的所有 services
kubectl get pods --all-namespaces             # 列出所有命名空间下的全部的 Pods
kubectl get pods -o wide                      # 列出当前命名空间下的全部 Pods，并显示更详细的信息
kubectl get deployment my-dep                 # 列出某个特定的 Deployment
kubectl get pods                              # 列出当前命名空间下的全部 Pods
kubectl get pod my-pod -o yaml                # 获取一个 pod 的 YAML
```

##### describe 命令的详细输出

```
kubectl describe nodes my-node
kubectl describe pods my-pod
```

##### 列出当前名字空间下所有 Services，按名称排序

```
kubectl get services --sort-by=.metadata.name
```

###### 列出 Pods，按重启次数排序

```
kubectl get pods --sort-by='.status.containerStatuses[0].restartCount'
```

##### 列举所有 PV 持久卷，按容量排序

```
kubectl get pv --sort-by=.spec.capacity.storage
```

##### 获取当前命名空间中正在运行的 Pods

```
kubectl get pods --field-selector=status.phase=Running
```

##### #####更新资源

```
kubectl set image deployment/frontend www=image:v2               # 滚动更新 "frontend" Deployment 的 "www" 容器镜像
kubectl rollout history deployment/frontend                      # 检查 Deployment 的历史记录，包括版本
kubectl rollout undo deployment/frontend                         # 回滚到上次部署版本
kubectl rollout undo deployment/frontend --to-revision=2         # 回滚到特定部署版本
kubectl rollout status -w deployment/frontend                    # 监视 "frontend" Deployment 的滚动升级状态直到完成
kubectl rollout restart deployment/frontend                      # 轮替重启 "frontend" Deployment
```

##### 对资源进行伸缩

```
kubectl scale --replicas=3 rs/foo                                 # 将名为 'foo' 的副本集伸缩到 3 副本
kubectl scale --replicas=3 -f foo.yaml                            # 将在 "foo.yaml" 中的特定资源伸缩到 3 个副本
kubectl scale --current-replicas=2 --replicas=3 deployment/mysql  # 如果名为 mysql 的 Deployment 的副本当前是 2，那么将它伸缩到 3
kubectl scale --replicas=5 rc/foo rc/bar rc/baz                   # 伸缩多个副本控制器
```

##### 与运行中的 Pods 进行交互

```
kubectl logs my-pod                                 # 获取 pod 日志（标准输出）
kubectl logs -l name=myLabel                        # 获取含 name=myLabel 标签的 Pods 的日志（标准输出）
kubectl logs my-pod --previous                      # 获取上个容器实例的 pod 日志（标准输出）
kubectl logs my-pod -c my-container                 # 获取 Pod 容器的日志（标准输出, 多容器场景）
kubectl logs -l name=myLabel -c my-container        # 获取含 name=myLabel 标签的 Pod 容器日志（标准输出, 多容器场景）
kubectl logs my-pod -c my-container --previous      # 获取 Pod 中某容器的上个实例的日志（标准输出, 多容器场景）
kubectl logs -f my-pod                              # 流式输出 Pod 的日志（标准输出）
kubectl logs -f my-pod -c my-container              # 流式输出 Pod 容器的日志（标准输出, 多容器场景）
kubectl logs -f -l name=myLabel --all-containers    # 流式输出含 name=myLabel 标签的 Pod 的所有日志（标准输出）
kubectl run -i --tty busybox --image=busybox -- sh  # 以交互式 Shell 运行 Pod
kubectl run nginx --image=nginx -n mynamespace      # 在指定名字空间中运行 nginx Pod
kubectl run nginx --image=nginx                     # 运行 ngins Pod 并将其规约写入到名为 pod.yaml 的文件
  --dry-run=client -o yaml > pod.yaml

kubectl attach my-pod -i                            # 挂接到一个运行的容器中
kubectl port-forward my-pod 5000:6000               # 在本地计算机上侦听端口 5000 并转发到 my-pod 上的端口 6000
kubectl exec my-pod -- ls /                         # 在已有的 Pod 中运行命令（单容器场景）
kubectl exec --stdin --tty my-pod -- /bin/sh        # 使用交互 shell 访问正在运行的 Pod (一个容器场景)
kubectl exec my-pod -c my-container -- ls /         # 在已有的 Pod 中运行命令（多容器场景）
kubectl top pod POD_NAME --containers               # 显示给定 Pod 和其中容器的监控数据
kubectl top pod POD_NAME --sort-by=cpu              # 显示给定 Pod 的指标并且按照 'cpu' 或者 'memory' 排序
```

##### 与 Deployments 和 Services 进行交互

```
kubectl logs deploy/my-deployment                         # 获取一个 Deployment 的 Pod 的日志（单容器例子）
kubectl logs deploy/my-deployment -c my-container         # 获取一个 Deployment 的 Pod 的日志（多容器例子）

kubectl port-forward svc/my-service 5000                  # 侦听本地端口 5000 并转发到 Service 后端端口 5000
kubectl port-forward svc/my-service 5000:my-service-port  # 侦听本地端口 5000 并转发到名字为 <my-service-port> 的 Service 目标端口

kubectl port-forward deploy/my-deployment 5000:6000       # 侦听本地端口 5000 并转发到 <my-deployment> 创建的 Pod 里的端口 6000
kubectl exec deploy/my-deployment -- ls                   # 在 Deployment 里的第一个 Pod 的第一个容器里运行命令（单容器和多容器例子）
```

##### 与节点和集群进行交互

```
kubectl cordon my-node                                                # 标记 my-node 节点为不可调度
kubectl drain my-node                                                 # 对 my-node 节点进行清空操作，为节点维护做准备
kubectl uncordon my-node                                              # 标记 my-node 节点为可以调度
kubectl top node my-node                                              # 显示给定节点的度量值
kubectl cluster-info                                                  # 显示主控节点和服务的地址
kubectl cluster-info dump                                             # 将当前集群状态转储到标准输出
kubectl cluster-info dump --output-directory=/path/to/cluster-state   # 将当前集群状态输出到 /path/to/cluster-state

# 如果已存在具有指定键和效果的污点，则替换其值为指定值。
kubectl taint nodes foo dedicated=special-user:NoSchedule
```



