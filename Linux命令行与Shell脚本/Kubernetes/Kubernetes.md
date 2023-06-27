## Kubernetes

> <img src="img\image-20221222162548403.png" alt="image-20221222162548403" style="zoom:80%;" /> 
>
> **Kubernetes组件**
>
> - 控制组件（Master节点）
> 
>   1. kube-apiserver
> 
>      > - 提供集群管理RestAPI接口，包括授权，数据校验，集群状态变更
>      >
>     > - 提供其他模块之间的数据交互和通信，实现SDK
> 
>  2. etcd：k8s集群后台key-value数据库
> 
>   3. kube-scheduler：负责监测或调度Pod在Node上运行
> 
>   4. kube-controller-manager：控制器
> 
>     > - Node Controller：负责在节点出现故障时进行通知和响应
>      > - Job Controller：负责创建资源任务
>     > - ServiceAccount：以命名空间创建的账号服务
>      > - EndpointSlice 
> 
> - 工作组件（Node节点）
> 
>   1. kubelet：在node上运行，控制容器运行Pod
>   2. kube-proxy：实现集群内部与Pod通讯
>   3. Container Runtime：容器环境（支持containerd，CRI-O)
> 
> - Addon插件（DaemonSet，Deployment等资源）
> 
>   1. DNS
> 2. Dashboard
>   3. log
> 4. CNI 网络插件（它们负责为 Pod 分配 IP 地址，并使这些 Pod 能在集群内部相互通信）
> 
> **K8s节点**：
> 
> - 节点状态 
> 
>   1. Ready：true表示正常，`False` 表示节点不健康而且不能接收 Pod
>   2. DiskPressure：true表示磁盘可用空间低
>   3. MemoryPressure：true表示内存可用空间低
>   4. PIDPressure：true表示节点进程过多
>   5. NetworkUnavailable：true表示网络配置不正确
> 
> - node操作
> 
>   ~~~shell
>   # 设置为不可调度
>   kubectl cordon <nodename>
>   kubectl uncordon <nodename>
>   #驱逐改节点上的pod
>   kubectl drain <nodename> --delete-local-data --force --ignore-daemonsets
>   # 移除节点
>   kubectl delete node <nodename> 
>   ~~~
> 
> 

