## K8s安装

> **K8s安装包**
>
> ![image-20230620171907421](img\image-20230620171907421.png) 
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
> ~~~
>
> 3. 修改Centos内核（非必要）
>
>    ~~~shell
>    cat > /etc/sysctl.d/k8s.conf <<'EOF'
>    net.ipv4.ip_forward = 1
>    net.bridge.bridge-nf-call-iptables = 1
>    net.bridge.bridge-nf-call-ip6tables = 1
>    net.ipv6.conf.all.disable_ipv6 = 1
>    fs.may_detach_mounts = 1
>    vm.overcommit_memory=1
>    vm.panic_on_oom=0
>    fs.inotify.max_user_watches=89100
>    fs.file-max=52706963
>    fs.nr_open=52706963
>    net.netfilter.nf_conntrack_max=2310720
>    net.ipv4.tcp_keepalive_time = 600
>    net.ipv4.tcp_keepalive_probes = 3
>    net.ipv4.tcp_keepalive_intvl =15
>    net.ipv4.tcp_max_tw_buckets = 36000
>    net.ipv4.tcp_tw_reuse = 1
>    net.ipv4.tcp_max_orphans = 327680
>    net.ipv4.tcp_orphan_retries = 3
>    net.ipv4.tcp_syncookies = 1
>    net.ipv4.tcp_max_syn_backlog = 16384
>    net.ipv4.ip_conntrack_max = 65536
>    net.ipv4.tcp_max_syn_backlog = 16384
>    net.ipv4.tcp_timestamps = 0
>    net.core.somaxconn = 16384
>    EOF
>    sysctl --system
>    
>    # 升级内核
>    wget http://193.49.22.109/elrepo/kernel/el7/x86_64/RPMS/kernel-ml-devel-4.19.12-1.el7.elrepo.x86_64.rpm
>    wget http://193.49.22.109/elrepo/kernel/el7/x86_64/RPMS/kernel-ml-4.19.12-1.el7.elrepo.x86_64.rpm
>    yum -y localinstall kernel-ml*
>    grubby --args="user_namespace.enable=1" --update-kernel="$(grubby --default-kernel)"
>    grubby --default-kernel
>    yum -y update --exclude=kernel*
>    # 安装ipvsadm等相关工具
>    yum -y install ipvsadm ipset sysstat conntrack libseccomp 
>    modprobe -- ip_vs
>    modprobe -- ip_vs_rr
>    modprobe -- ip_vs_wrr
>    modprobe -- ip_vs_sh
>    modprobe -- nf_conntrack
>    
>    cat > /etc/modules-load.d/ipvs.conf << 'EOF'
>    ip_vs
>    ip_vs_lc
>    ip_vs_wlc
>    ip_vs_rr
>    ip_vs_wrr
>    ip_vs_lblc
>    ip_vs_lblcr
>    ip_vs_dh
>    ip_vs_sh
>    ip_vs_fo
>    ip_vs_nq
>    ip_vs_sed
>    ip_vs_ftp
>    ip_vs_sh
>    nf_conntrack
>    ip_tables
>    ip_set
>    xt_set
>    ipt_set
>    ipt_rpfilter
>    ipt_REJECT
>    ipip
>    EOF
>    # 检查默认加载的内核版本
>    grubby --default-kernel
>    # 检查支持ipvs的内核模块
>    lsmod | grep --color=auto -e ip_vs -e nf_conntrack
>    uname -r
>    ~~~
>
> 4. k8s组件安装
>
>    ~~~shell
>    # containerd模块
>    cat >/etc/modules-load.d/containerd.conf<<'EOF'
>    overlay
>    br_netfilter
>    EOF
>    systemctl restart systemd-modules-load.service
>    
>    cat >/etc/sysctl.d/99-kubernetes-cri.conf<<'EOF'
>    net.bridge.bridge-nf-call-iptables  = 1
>    net.ipv4.ip_forward                 = 1
>    net.bridge.bridge-nf-call-ip6tables = 1
>    EOF
>    
>    # 设置阿里云containerd仓库
>    wget -O /etc/yum.repos.d/docker-ce.repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
>    # 安装 containerd
>    yum install -y containerd.io
>    # 修改containerd配置
>    mkdir /etc/containerd -p && containerd config default > /etc/containerd/config.toml
>    #生成配置文件
>    containerd config default > /etc/containerd/config.toml
>    #编辑配置文件
>    vim /etc/containerd/config.toml
>    # 设置 SystemdCgroup = true
>    # 设置 sandbox_image = "registry.aliyuncs.com/google_containers/pause:3.6"
>    # 启动containerd
>    systemctl enable --now  containerd
>    systemctl status containerd
>    # 验证
>    ctr version
>    runc -version
>    ~~~
>
> 5. 生成配置证书
>
>    ~~~shell
>    # cfssl  cfssljson 工具下载
>    wget "https://pkg.cfssl.org/R1.2/cfssl_linux-amd64" -O /usr/local/bin/cfssl
>    wget "https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64" -O /usr/local/bin/cfssljson
>    chmod +x /usr/local/bin/cfssl /usr/local/bin/cfssljson
>    
>    # cfssl 配置文件
>    https://gitee.com/dukuan/k8s-ha-install/tree/manual-installation-v1.27.x/pki
>    
>    # 生成etcd证书
>    cfssl gencert -initca etcd-ca-csr.json | cfssljson -bare /etc/etcd/ssl/etcd-ca
>    
>    # 配置etcd证书 
>    cfssl gencert \
>       -ca=/etc/etcd/ssl/etcd-ca.pem \
>       -ca-key=/etc/etcd/ssl/etcd-ca-key.pem \
>       -config=ca-config.json \
>       -hostname=k8s1,k8s2,k8s3,k8s4,k8s5,k8s6,k8s7,k8s8,k8s9\
>       -profile=kubernetes \
>       etcd-csr.json | cfssljson -bare /etc/etcd/ssl/etcd
>    
>    #配置k8s证书
>    cfssl gencert -initca ca-csr.json | cfssljson -bare /etc/kubernetes/pki/ca
>    
>    # 生成apiserver客户端证书  10.96.0.0为apiserver网段，192.168.31.100宿主机IP
>    cfssl gencert   -ca=/etc/kubernetes/pki/ca.pem   -ca-key=/etc/kubernetes/pki/ca-key.pem   -config=ca-config.json   -hostname=10.96.0.0,192.168.31.100,k8s1,k8s2,k8s3,k8s4,k8s5,k8s6,k8s7,k8s8,k8s9  -profile=kubernetes   apiserver-csr.json | cfssljson -bare /etc/kubernetes/pki/apiserver
>    
>    # 生成front-proxy证书
>    cfssl gencert   -initca front-proxy-ca-csr.json | cfssljson -bare /etc/k8s/pki/front-proxy-ca 
>    cfssl gencert   -ca=/etc/k8s/pki/front-proxy-ca.pem   -ca-key=/etc/k8s/pki/front-proxy-ca-key.pem   -config=ca-config.json   -profile=kubernetes   front-proxy-client-csr.json | cfssljson -bare /etc/k8s/pki/front-proxy-client
>    
>    # 生成controller manager证书
>    
>    ~~~
>
>    

