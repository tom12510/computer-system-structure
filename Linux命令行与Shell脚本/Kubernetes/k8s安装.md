## K8s安装

> **K8s安装包**
>
>  ![image-20230620171907421](img\image-20230620171907421.png) 
>
> 1. 服务器环境配置
>
>    ~~~shell
>    # 设置主机名称（临时配置）
>    vim /etc/hostname  && hostnamectl set-hostname k8s1
>    
>    # 配置DNS解析 并同步到其他节点
>    cat >> /etc/hosts  << 'EOF'
>     192.168.31.100 k8s1
>     192.168.31.101 k8s2
>     192.168.31.102 k8s3
>     192.168.31.103 k8s4
>     192.168.31.104 k8s5
>    EOF
>    
>    #所有节点修改yum镜像
>    curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
>    sed -i -e '/mirrors.cloud.aliyuncs.com/d' -e '/mirrors.aliyuncs.com/d' /etc/yum.repos.d/CentOS-Base.repo
>    curl -o /etc/yum.repos.d/docker-ce.repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
>    
>    #安装常用软件
>    yum -y install bind-utils expect rsync wget jq psmisc vim net-tools telnet yum-utils device-mapper-persistent-data lvm2 git ntpdate
>    
>    #免密登陆
>    ssh-keygen -t rsa 
>    ssh-copy-id -i /root/.ssh/id_rsa.pub  <remote-ip>
>    
>    # 使用阿里云同步时间
>    ntpdate ntp.aliyun.com
>    ~~~
>
> 2. 服务器应用配置
>
>    ~~~shell
>    # 所有节点关闭firewalld，selinux，NetworkManager，swap
>    systemctl disable --now firewalld 
>    systemctl disable --now NetworkManager
>    setenforce 0
>    sed -i 's#SELINUX=enforcing#SELINUX=disabled#g' /etc/sysconfig/selinux
>    sed -i 's#SELINUX=enforcing#SELINUX=disabled#g' /etc/selinux/config
>    swapoff -a && sysctl -w vm.swappiness=0
>    sed -ri '/^[^#]*swap/s@^@#@' /etc/fstab
>    
>    ~~~
>
>    

