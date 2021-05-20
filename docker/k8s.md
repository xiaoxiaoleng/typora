##### 查看有哪些服务实例

```shell
kubectl get pod
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