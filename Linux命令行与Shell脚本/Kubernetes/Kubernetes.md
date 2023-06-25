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
> - #### Control Plane Components 控制组件
>
>   > *控制平面组件会为集群做出全局决策，资源的调度。 以及检测和响应集群事件*
>   >
>   > -  kube-apiserver    k8s 公共api组件
>   >
>   > -  etcd 集群数据的后台数据库
>   >
>   > - kube-scheduler  负责监视新创建的、未指定运行节点的Pods，并选择节点来让 Pod 在上面运行
>   >
>   > - kube-controller-manager 负责运行控制器进程（管理集群节点进程）
>   >
>   >   > 节点控制器：负责在节点出现故障时进行通知和响应
>   >   >
>   >   > 任务控制器：监测代表一次性任务的 Job 对象，然后创建 Pods 来运行这些任务直至完成
>   >   >
>   >   > 端点分片控制器
>   >   >
>   >   > 服务账号控制器：为新的命名空间创建默认的服务账号
>   >
>
> - #### **Node组件**
>
>   > *负责维护运行的 Pod 并提供 Kubernetes 运行环境*
>   >
>   > - kubelet ：接收一组通过各类机制提供给它的 PodSpecs， 确保这些 PodSpecs 中描述的容器处于运行状态且健康
>   >
>   > - kube-proxy：维护节点上的一些网络规则， 这些网络规则会允许从集群内部或外部的网络会话与 Pod 进行网络通信
>
> - #### AddonS 插件
>
>   > DNS 
>   >
>   > Web仪表盘
>   >
>   > 容器资源监控
>   >
>   > 日志
>
> #### K8S对象
>
> > K8S资源实例，通过yaml进行声明对象，通过kubectl管理实例操作
> >
> > - 集群中运行了哪些容器化应用程序（以及在哪个节点上运行）
> > - 集群中对应用程序可用的资源（网络，存储等）
> > - 应用程序相关的策略定义，例如，重启策略、升级策略、容错策略
> > - 其他Kubernetes管理应用程序时所需要的信息
> >
> > K8S对象状态
> >
> > - spec 期望目标状态
> > - status 实际状态（Kubernetes 监听）
> >
> > **K8s对象配置**
> >
> > ~~~yaml
> > # 创建该对象所使用的 Kubernetes API 的版本
> > apiVersion: apps/v1
> > # 想要创建的对象的类别 
> > # Endpoints：引用外部资源 
> > # Service：部署一个内部虚拟IP应用，可以进行端口映射
> > # Secrets 存储和管理一些敏感数据，比如密码，token，密钥等敏感信息
> > # Deployment 部署一个Pod，内部只能链接service，无法互相链接
> > kind: Deployment 
> > # 标识对象的一些数据（唯一）
> > metadata:
> >   name: nginx-deployment (唯一)
> > # Pod的详细定义
> > spec:
> >   replicas: 2 # 告知 Deployment 运行 2 个与该模板匹配的 Pod
> >   strategy:
> >     rollingUpdate:
> >       maxSurge: 1 #滚动升级时会先启动1个pod
> >       maxUnavailable: 1 #滚动升级时允许的最大Unavailable的pod个数
> >   selector:  #定义标签选择器
> >     matchLabels: #定义匹配标签
> >       app: nginx  # pod标签一致
> >       
> >   template:  # 业务模板
> >     metadata:
> >       labels:
> >         app: nginx  # 模板名称
> >     spec:  #定义容器属性
> >       imagePullSecrets:
> >       - name: #拉取密钥
> >       containers: # 容器配置
> >       - name: nginx
> >         image: nginx:1.14.2 # 定义容器使用的镜像以及版本
> >         # command和args不存在则使用Docker默认启动配置 存在则忽略Docker配置
> >         command: []   #启动命令
> >         args:  # 启动参数
> >          - ''
> >         ports: 
> >         - containerPort: 80 #定义容器的对外的端口
> >         # 拉取方式
> >         # Always：总是拉取pull IfNotPresent：默认值,本地有则使⽤本地镜像,不拉取
> >         # Never：只使⽤本地镜像，从不拉取
> >         imagePullPolicy: Always
> >         #Pod存活探针，判断容器是否存活,如果LivenessProbe失败,随后kubelet将kill掉container
> >         # 并根据RestarPolicy进行进一步的操作
> >         livenessProbe: 
> >           # http方式判断
> >           httpGet:
> >             path: /health  #心跳监测路径
> >             port: 8080
> >             scheme: HTTP
> >            initialDelaySeconds: 60 #启动后延时多久开始运行检测
> >            timeoutSeconds: 5  # 等待超时时间
> >            successThreshold: 1 # 连接成功次数
> >            failureThreshold: 5 # 失败重试次数
> >         readinessProbe: #就绪探针，判断容器中应用是否存活
> >            initialDelaySeconds: 10
> >            periodSeconds: 5 # 检测时间间隔
> >            # 使用命令方式探针检测（命令中不能有空格）
> >            exec:
> >              command:
> >              - cat 
> >              - /tmp/healthy
> >         resources:  # 对象资源限制
> >          limits:
> >             cpu: "1"
> >             memory: "400Mi"
> >           requests:
> >             cpu: "0.5"
> >             memory: "200Mi"
> >             
> >         env: #通过环境变量的方式
> >           - name:
> >             value:
> >         volumeMounts: #挂载volumes中定义的磁盘
> >         
> > ~~~
> >
> > K8S对象管理
> >
> > ~~~sh
> > # 配置文件生成
> > kubectl apply -f https://k8s.io/examples/application/deployment.yaml
> > # 创建配置文件中定义的对象
> > kubectl create deployment nginx --image nginx
> > # 删除两个配置文件中定义的对象：
> > kubectl delete -f nginx.yaml -f redis.yaml
> > # 通过覆盖活动配置来更新配置文件中定义的对象：
> > kubectl replace -f nginx.yaml
> > ~~~
> >
> > 
>
> 

