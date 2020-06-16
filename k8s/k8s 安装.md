# k8s 集群安装

### 设置系统主机名以及 Host 文件的相互解析

```shell
hostnamectl set-hostname k8s-master # 设置系统主机名，然后去 /etc/hosts 配置对应的解析域名
```

### 安装依赖包

```shell
yum install -y conntrack ntpdate ntp ipvsadm ipsec jq iptables curl sysstat libseccomp wget vim net-tools git
```

### 设置防火墙为 iptables 并设置空规则

```shell
systemctl stop firewalld && systemctl disable firewalld
yum install -y iptables-services && systemctl start iptables && systemctl enable iptables && iptables -F && service iptables save
```

### 关闭 selinux

```shell
swapoff -a && sed i '/ swap / s/^\(.*)\$/#\1/g' /etc/fstab # 关闭 swap 分区，永久关闭虚拟内存，避免使用虚拟内存，pod 可能会在虚拟内存运行，降低效率
setenforce 0 && sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
```

### 调整内核参数

```shell
cat > kubernetes.conf <<EOF
net.bridge.bridge-nf-call-iptables=1  # 开启网桥模式
net.bridge.bridge-nf-call-ip6tables=1 	# 开启网桥模式
net.ipv6.conf.all.disable_ipv6=1	# 禁用 ipv6 协议，这三条必须配置
net.ipv4.ip_forward=1	# 禁用ipv4转发
net.netfilter.nf_conntrack_max=2310720
vm.swappiness=0 # 禁止使用 swap 空间，只用当系统 OOM 时才允许使用
vm.overcommit_memory=1 # 不检查物理内存是否够用
vm.panic_on_oom=0	# 开启 oom
fs.file-max=52706963 # 文件句柄数
fs.nr_open=52706963 # 文件最大的打开数
EOF
cp kubernetes.conf /etc/sysctl.d/kubernetes.conf # 复制到此目录下，使得能够开机调用
sysctl -p /etc/sysctl.d/kubernetes.conf # 立即调用
```

### 调整时区

```shell
timedatectl set-timezone Asia/Shanghai
# 将当前的 UTC 时间写入硬件时钟
timedatectl set-local-rtc 0
# 重启依赖于系统时间的服务
systemctl restart rsyslog
systemctl restart crond
```

### 关闭不需要的服务

```shell
systemctl stop postfix && systemctl disable postfix
```

### 设置日志

```shell
mkdir /var/log/journal # 持久化保存日志的目录
mkdir /etc/systemd/journald.conf.d
cat > /etc/systemd/journald.conf.d/99-prophet.conf <<EOF
[Journal]
# 持久化保存到磁盘
Storage=persistent

#压缩历史日志
Compress=yes

SyncIntervalSec=5m
RateLimitInterval=30s
RateLimitBurst=1000

#最大占用空间 10G
SystemMaxUse=10G

#单日志文件最大 200M
SystemMaxFileSize=200M
#日志保存时间 2 周
MaxRetentionSec=2week
#不将日志转发到 syslog
ForwardToSyslog=no
EOF

systemctl restart systemd-journald
```

### 升级内核为 4.44 

```shell
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0.3.el7.elrepo.noarch.rpm
yum --enablerepo=elrepo-kernel install -y kernel-lt
# 设置开机从新内核启动
grub2-set-default "Centos Linux (4.4.182-1.el7.elrepo.x86_64) 7 (Core)"
```

### kube-proxy 开启 ipvs 的前置条件

```shell
modprobe br_netfilter

cat > /etc/sysconfig/modules/ipvs.modules <<EOF
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
EOF
chmod 755 /etc/sysconfig/modules/ipvs.modules && bash
/etc/sysconfig/modules/ipvs.modules && lsmod | grep -e ip_vs -e nf_conntrack_ipv4
```

### 安装 docker

```shell
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manage --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
yum update -y && yum install -y docker-ce

makdir /etc/docker
cat > /etc/docker/daemon.json <<EOF
{
	"exec-opts":["native.cgroupdriver=systemd"],
	"log-dirver":"json-file",
}
```

### 安装 kubeadm （主从配置）

```shell
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

yum install -y kubeadm kubectl kubelet
systemctl enable kubelet.service
```

### 生成初始化文件

```shell
kubeadm config print init-defaults > kubeadm-init.yaml

# 需要修改init配置文件的地方
advertiseAddress: 1.2.3.4 #将此地址修改为本机地址（内网地址）
imagesRepository: k8s.gcr.io # 将此地址修改为可以使用的镜像地址，registry.cn-hangzhou.aliyuncs.com/mxy
nodeRegistration: name # 如果是 master 节点改为 k8s-master,其余的节点按照对应的名字修改
networking:
  podSubnet: 10.244.0.0/16 # 此处是因为会添加一个 flannel 插件它的网段是在 10.244.0.0/16 所以提前配置
  
---  # 修改默认的调度方式，改为 ipvs
apiVersion: kubeProxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
featureGates:
  SupportIPVSProxyMode: true
mode: ipvs

kubeadm config images pull --config kubeadm-init.yaml 	# 拉取镜像
kubeadm init --config kubeadm-init.yaml | tee kubeadm-init.log # 初始化，输出重定向到 kubeadm-init.log 中

# 初始化完成之后
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# 部署 flannel 网络
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

# 然后复制日志输出中的 join 命令，在其他机器中将其余的节点加入
```

#### 初始化遇到的错误

```
错误：filecontent--proc-sys-net-ipv4-ip_forward /proc/sys/net/ipv4/ip_forward contents are not set to 1
修复：echo 1 > /proc/sys/net/ipv4/ip_forward
```



