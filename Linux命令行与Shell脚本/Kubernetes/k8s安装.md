## K8s安装

> **K8s安装包**
>
> ![image-20230618151132098](img\image-20230618151132098.png) 
>
> **K8s PKI证书** ：用于集群节点之间安全通信
>
> - 默认存放在 `/etc/kubernetes/pki`目录中，或使用 --cert-dir参数指定目录，用于账户认证证书存放在 `/etc/kubernetes` 中
>
> - 组件证书包含
>
>   ```shell
>    # Kubernetes 通用 CA
>   /etc/kubernetes/pki/ca.crt
>   /etc/kubernetes/pki/ca.key
>   # 与 etcd 相关的所有功能
>   /etc/kubernetes/pki/etcd/ca.crt
>   /etc/kubernetes/pki/etcd/ca.key
>   # 用于前端代理
>   /etc/kubernetes/pki/front-proxy-ca.crt
>   /etc/kubernetes/pki/front-proxy-ca.key
>   # 用户证书
>   /etc/kubernetes/sa.key
>   /etc/kubernetes/sa.pub
>   ```
>
> - 查看证书是否过期   kubeadm certs check-expiration
>
> - kubeadm组件升级会延长证书时间  kubeadm upgrade apply
>
> - 手动更新证书  kubeadm certs renew

