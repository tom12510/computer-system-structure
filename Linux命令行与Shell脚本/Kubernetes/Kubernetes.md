## Kubernetes

> <img src="img\image-20221222162548403.png" alt="image-20221222162548403" style="zoom:80%;" /> 
>
> **Master节点**
>
> > K8s集群控制节点，包含APIServer，Scheduler，ControllerManager，Etcd组件
> >
> > - APIServer：提供集群各模块之间数据交换，并将集群状态和信息存储到Etcd中，通过RestAPI接口操作集群对象资源
> > - Scheduler：Pod调度中心，将Pod分配到合理的Node节点
> > - ControllerManager：Pod自动修复控制，提供Pod创建，更新于删除
> > - Etcd：分布式key-value数据存储组件
>
> **Node节点**
>
> > 部署kubelet的宿主机，运行监听和运行容器
> >
> > - Kubelet：负责与Mastet节点通信，管理该节点上的Pod
> > - Kube-Proxy：负责Pod之间的通信和负载均衡
> > - container：容器环境
>
> **Pod**
>
> > 包含一个或多个应用程序的容器，同一个Pod共享存储空间，网络资源
> >
> > Pod状态
> >
> > 1. Pending：Pod 已被 Kubernetes 系统接受。当未创建。此阶段包括等待 Pod 被调度的时间和通过网络下载镜像的时间。可通过descrube查看原因
> > 2. Running：Pod已创建并已分配到Node运行
> > 3. Succeeded：Pod与停止运行，并且不再重启
> > 4. Failed：所有Pod异常终止
> > 5. Unkonw：无法获取Pod状态
> >
> > Pod探针：识别Pod所处状态
> >
> > 1. ExecAction：执行指定命令，返回值为0，认为Pod正常
> > 2. TcpSocketAction：检查容器指定端口
> > 3. HttpGetAction：指定Url进行get请求，状态码判断状态码是否正常
> > 4. livenessProbe：重启策略
> > 5. readinessProbe：判断程序是否异常
> >
> > Pod拉取于重启策略
> >
> > - 拉取策略：Always/Never/IfNotPersent：当容器tag为laster时处于Always状态
> > - 重启策略：Always/OnFailure/Never
> >
> > **Pod资源配置**
> >
> > ~~~yaml
> > apiVersion: v1   #必选，版本号，例如v1
> > kind: Pod        #必选，资源类型，例如 Pod
> > metadata:        #必选，元数据
> >   name: nginx   #必选，Pod名称
> >   namespace: string  #可选 Pod所属的命名空间,默认为"default"
> >   labels:       　　 #可选 自定义标签列表，用于Selector
> >     - app: nginx
> >   annotations: # 注释列表
> >     - app: ngix
> > spec:  #必选，Pod中容器的详细定义
> >   containers:  #必选，Pod中容器列表
> >   - name: string   #必选，容器名称
> >     image: string  #必选，容器的镜像名称
> >     imagePullPolicy: [ Always|Never|IfNotPresent ]  #获取镜像的策略 
> >     command: [string]   #容器的启动命令列表，如不指定，使用打包时使用的启动命令
> >     args: [string]      #容器的启动命令参数列表
> >     workingDir: string  #容器的工作目录
> >     volumeMounts:       #挂载到容器内部的存储卷配置
> >     - name: string      #引用pod定义的共享存储卷的名称，需用volumes[]部分定义的的卷名
> >       mountPath: string #存储卷在容器内mount的绝对路径，应少于512字符
> >       readOnly: boolean #是否为只读模式
> >     ports: #需要暴露的端口库号列表
> >     - name: string        #端口的名称
> >       containerPort: int  #容器需要监听的端口号
> >       hostPort: int       #容器所在主机需要监听的端口号，默认与Container相同
> >       protocol: string    #端口协议，支持TCP和UDP，默认TCP
> >     env:   #容器运行前需设置的环境变量列表
> >     - name: string  #环境变量名称
> >       value: string #环境变量的值
> >     resources: #资源限制和请求的设置
> >       limits:  #资源限制的设置
> >         cpu: string     #Cpu的限制，单位为core数，将用于docker run --cpu-shares参数
> >         memory: string  #内存限制，单位可以为Mib/Gib，将用于docker run --memory参数
> >       requests: #资源请求的设置
> >         cpu: string    #Cpu请求，容器启动的初始可用数量
> >         memory: string #内存请求,容器启动的初始可用数量
> >     lifecycle: #生命周期钩子
> > 		postStart: #容器启动后立即执行此钩子,如果执行失败,会根据重启策略进行重启
> > 		preStop: #容器终止前执行此钩子,无论结果如何,容器都会终止
> >     livenessProbe:  #对Pod内各容器健康检查的设置，当探测无响应几次后将自动重启该容器
> >       exec:       　 #对Pod容器内检查方式设置为exec方式
> >         command: [string]  #exec方式需要制定的命令或脚本
> >       httpGet:       #对Pod内个容器健康检查方法设置为HttpGet，需要制定Path、port
> >         path: string
> >         port: number
> >         host: string
> >         scheme: string
> >         HttpHeaders:
> >         - name: string
> >           value: string
> >       tcpSocket:     #对Pod内个容器健康检查方式设置为tcpSocket方式
> >          port: number
> >        initialDelaySeconds: 0       #容器启动完成后首次探测的时间，单位为秒
> >        timeoutSeconds: 0    　　    #对容器健康检查探测等待响应的超时时间，单位秒，默认1秒
> >        periodSeconds: 0     　　    #对容器监控检查的定期探测时间设置，单位秒，默认10秒一次
> >        successThreshold: 0
> >        failureThreshold: 0
> >        securityContext:
> >          privileged: false
> >   restartPolicy: [Always | Never | OnFailure]  #Pod的重启策略
> >   nodeName: <string> #设置NodeName表示将该Pod调度到指定到名称的node节点上
> >   nodeSelector: obeject #设置NodeSelector表示将该Pod调度到包含这个label的node上
> >   imagePullSecrets: #Pull镜像时使用的secret名称，以key：secretkey格式指定
> >   - name: string
> >   hostNetwork: false   #是否使用主机网络模式，默认为false，如果设置为true，表示使用宿主机网络
> >   volumes:   #在该pod上定义共享存储卷列表
> >   - name: string    #共享存储卷名称 （volumes类型有很多种）
> >     emptyDir: {}       #类型为emtyDir的存储卷，与Pod同生命周期的一个临时目录。为空值
> >     hostPath: string   #类型为hostPath的存储卷，表示挂载Pod所在宿主机的目录
> >       path: string      　　        #Pod所在宿主机的目录，将被用于同期中mount的目录
> >     secret:       　　　#类型为secret的存储卷，挂载集群与定义的secret对象到容器内部
> >       scretname: string  
> >       items:     
> >       - key: string
> >         path: string
> >     configMap:         #类型为configMap的存储卷，挂载预定义的configMap对象到容器内部
> >       name: string
> >       items:
> >       - key: string
> >         path: string
> > ~~~
>
> - Replication Controller与 ReplicaSet
>
>   > RC：控制Pod副本数达到期望值，当Pod失败，删除时自动替换（实际运行的Pod只有一个）
>   >
>   > RS：使用Deloyment对Pod进行创建，删除，和更新
>
> - 
>
> **Kubernetes组件**
>
> - 控制组件（Master节点）
> 
>   1. kube-apiserver
> 
>      > - **提供集群管理RestAPI接口**，包括授权，数据校验，集群状态变更
>      >
>      > - 提供其他模块之间的数据交互和通信，实现SDK
> 
>   2. etcd：k8s集群后台key-value数据库
> 
>   3. kube-scheduler：负责监测或调度Pod在Node上运行
> 
>   4. kube-controller-manager：控制器
> 
>      > - Node Controller：负责在节点出现故障时进行通知和响应
>      > - Job Controller：负责创建资源任务
>      > - ServiceAccount：以命名空间创建的账号服务
>      > - EndpointSlice 
>
> - 工作组件（Node节点）
>
>   1. kubelet：在node上运行，控制容器运行Pod
>   2. kube-proxy：实现集群内部与Pod通讯
>   3. Container Runtime：容器环境（支持containerd，CRI-O)
> 
> - Addon插件
>
>   1. DNS
>
>   2. Dashboard
> 
>   3. log
> 
>   4. 网络插件
> 
>      
