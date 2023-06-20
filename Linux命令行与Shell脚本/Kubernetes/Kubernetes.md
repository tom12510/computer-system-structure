## Kubernetes

> <img src="img\image-20221222162548403.png" alt="image-20221222162548403" style="zoom:80%;" /> 
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

