## 虚拟机准备
```shell
# virtualbox 下载；https://download.virtualbox.org/virtualbox/7.0.6/VirtualBox-7.0.6-155176-OSX.dmg
# centos min : http://mirrors.aliyun.com/centos/7.9.2009/isos/x86_64/CentOS-7-x86_64-Minimal-2009.iso?spm=a2c6h.25603864.0.0.60196aeaRSRcS0
# k8s-master (bridge)(192.168.1.20)
# k8s-node01 (bridge)(192.168.1.31)
# k8s-node02 (bridge)(192.168.1.32)
```

## 系统初始化
```shell
# 设置系统主机名以及 Host文件的相互解析
hostnamectl set-hostname k8s-master01

# 安装依赖包
yum install -y conntrack ntpdate ntp ipvsadm ipset jq iptables curl sysstat libseccomp wget vim net-tools git

# 设置防火墙为 Iptables 并设置空规则
systemctl stop firewalld && systemctl disable firewalld 
yum -y install iptables-services && systemctl start iptables && systemctl enable iptables && iptables -F && service iptables save

# 关闭SELINUX
swapoff -a && sed -i '/ swap /s/^\(.*\)$/#\1/g' /etc/fstab && setenforce 0 && sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config

# 调整内核参数，对于K8S
cat > kubernetes.conf <<EOF
net.bridge.bridge-nf-call-iptables=1
net.bridge.bridge-nf-call-ip6tables=1
net.ipv4.ip_forward=1
net.ipv4.tcp_tw_recycle=0
vm.swappiness=0 #禁止使用swap空间，只有当系统OOM时才允许使用它 
vm.overcommit_memory=1 # 不检查物理内存是否够用
vm.panic_on_oom=0 # 开启 OOM
fs.inotify.max_user_instances=8192
fs.inotify.max_user_watches=1048576
fs.file-max=52706963
fs.nr_open=52706963
net.ipv6.conf.all.disable_ipv6=1
net.netfilter.nf_conntrack_max=2310720
EOF
cp kubernetes.conf /etc/sysctl.d/kubernetes.conf
sysctl -p /etc/sysctl.d/kubernetes.conf  # sysctl: cannot stat /proc/sys/net/bridge/bridge-nf-call-iptables: 没有那个文件或目录 ----》 modprobe br_netfilter

# 调整系统时区
#设置系统时区为中国/上海
timedatectl set-timezone Asia/Shanghai
# 将当前的UTC时间写入硬件时钟
timedatectl set-local-rtc 0
# 重启依赖于系统时间的服务
systemctl restart rsyslog
systemctl restart crond

#关闭系统不需要服务
systemctl stop postfix && systemctl disable postfix

# 设置 rsyslogd 和 systemd journald
mkdir /var/log/journal # 持久化保存日志的目录
mkdir /etc/systemd/journald.conf.d
cat > /etc/systemd/journald.conf.d/99-prophet.conf <<EOF
[Journal]
#持久化保存到磁盘
Storage=persistent
#压缩历史日志
Compress=yes
SyncIntervalSec=5m
RateLimitInterval=30s
RateLimitBurst=1000
#最大占用空间10G
SystemMaxUse=10G
#单日志文件最大200M
SystemMaxFileSize=200M
#日志保存时间2周
MaxRetentionSec=2week 
#不将日志转发到 syslog 
ForwardToSyslog=no
EOF
systemctl restart systemd-journald

# 升级系统内核为4.44
# CentOS 7.x系统自带的3.10.x内核存在一些Bugs，导致运行的Docker、Kubernetes不稳定，例如：rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
    # 方式一：最新的
    rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
    yum --enablerepo=elrepo-kernel install -y kernel-lt

    # 方式二：google 下载rpm包进行安装
    wget https://linux.cc.iitk.ac.in/mirror/centos/elrepo/kernel/el7/x86_64/RPMS/kernel-lt-4.4.213-1.el7.elrepo.x86_64.rpm
    yum  install  kernel-lt-4.4.213-1.el7.elrepo.x86_64.rpm

    # 查看已经安装的内核
    1： awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg
    2： # 安装完成后检查 /boot/grub2/grub.cfg 中对应内核menuentry中是否包含initrd16配置，如果没有，再安装一次！
#设置开机从新内核启动
# grub2-set-default 'CentOS Linux (4.4.189-1.el7.elrepo.x86_64) 7 (Core)'
grub2-set-default 'CentOS Linux (4.4.213-1.el7.elrepo.x86_64) 7 (Core)'
```

## KUBEADM部署安装
```shell
# kube-proxy开启ipvs的前置条件
modprobe br_netfilter
cat > /etc/sysconfig/modules/ipvs.modules <<EOF
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr 
modprobe -- ip_vs_sh 
modprobe -- nf_conntrack_ipv4
EOF
chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules && lsmod | grep -e ip_vs -e nf_conntrack_ipv4

# 安装 Docker软件
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
yum update -y && yum install -y docker-ce
## 创建/etc/docker目录 
mkdir /etc/docker
#配置daemon.
cat > /etc/docker/daemon.json <<EOF
{
    "exec-opts":["native.cgroupdriver=systemd"],
    "registry-mirrors": ["https://ud6340vz.mirror.aliyuncs.com"],
    "log-driver":"json-file",
    "log-opts":{
        "max-size":"100m"
    }
}
EOF
mkdir -p /etc/systemd/system/docker.service.d
# 重启docker服务
systemctl daemon-reload && systemctl restart docker && systemctl enable docker

# 安装Kubeadm（主从配置）
cat > /etc/yum.repos.d/kubernetes.repo <<EOF 
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
yum -y install kubeadm-1.15.1 kubectl-1.15.1 kubelet-1.15.1
systemctl enable kubelet.service

# 初始化主节点 另外一种初始化方式参见（https://k8s.easydoc.net/docs/dRiQjyTY/28366845/6GiNOzyZ/nd7yOvdY）
kubeadm config print init-defaults > kubeadm-config.yaml
kubeadm init --config=kubeadm-config.yaml --experimental-upload-certs | tee kubeadm-init.log
kubeadm init --pod-network-cidr=10.244.0.0/16 --image-repository=registry.aliyuncs.com/google_containers
# 忘记了重新获取：
kubeadm token create --print-join-command
# 加入主节点以及其余工作节点
# 执行安装日志中的加入命令即可
# 部署网络
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
# NotReady （https://stackoverflow.com/questions/52675934/network-plugin-is-not-ready-cni-config-uninitialized）
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/62e44c867a2846fefb68bd5f178daf4da3095ccb/Documentation/kube-flannel.yml# NotReady （https://stackoverflow.com/questions/52675934/network-plugin-is-not-ready-cni-config-uninitialized）
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/62e44c867a2846fefb68bd5f178daf4da3095ccb/Documentation/kube-flannel.yml

# 重置k8s集群
sudo kubeadm reset && rm -rf /var/lib/cni/ && sudo rm -rf /var/lib/cni/ && systemctl daemon-reload && systemctl restart kubelet &&
sudo iptables -F && sudo iptables -t nat -F && sudo iptables -t mangle -F && sudo iptables -X

# 查看当前集群配置
kubeadm config view

# 安装dashboard
wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.4.0/aio/deploy/recommended.yaml
```


## Reference
* https://zhuanlan.zhihu.com/p/579730438
* https://stackoverflow.com/questions/53525975/kubernetes-error-uploading-crisocket-timed-out-waiting-for-the-condition
* https://blog.csdn.net/qq_29385297/article/details/127682552
* https://blog.csdn.net/Wuli_SmBug/article/details/104712653
* https://stackoverflow.com/questions/52675934/network-plugin-is-not-ready-cni-config-uninitialized
* https://lzwgiter.github.io/posts/eed4a979.html#/flannel%E7%BD%91%E7%BB%9C%E6%8F%92%E4%BB%B6---error-registering-network-failed-to-acquire-lease-node-xxx-pod-cidr-not-assigned
* https://blog.csdn.net/weixin_43822977/article/details/118942882
* https://stackoverflow.com/questions/58276969/k8s-convert-kubeadm-init-command-line-arguments-to-config-yaml
* https://blog.csdn.net/qq_21816375/article/details/79193011