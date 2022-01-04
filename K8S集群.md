### K8S集群实践
#### 虚拟机安装
- 使用VMware创建虚拟机; 选择安装程序光盘镜像文件(提前下载CentOS7.iso)
- 使用典型安装模式; 设置虚拟机名称; 并选择创建位置, 放在剩余空间大的磁盘下
- 将虚拟磁盘存储为单个文件; 自定义硬件: 2核/2G; 网络模式: 桥接模式
- 安装语言: 中文; 软件选择: 开发及生成工作站; ROOT密码; 创建用户;

#### 部署虚拟机centos7环境
- VMware - 编辑 -虚拟网络编辑 -选择桥接模式 -桥接至主机网络适配器上
- dhclient命令生成ip，然后修改vim /etc/sysconfig/network-scripts/ifcfg-ens33
- ps -ef | grep dhclient 查看进程 然后 kill -9 进程id 关闭该进程
- 配置静态ip地址，注意的是先查看主机ip地址信息然后配置ip，子网掩码，网关，dns; 虚拟机网络适配器选桥接模式(不勾选复制...)
- 配置静态ip后, 虚拟机linux执行systemctl restart network.service就可以连接外网
- 虚拟机上网，主机与虚拟机互相ping，才算成功；一般情况下主机都能ping通虚拟机，只是虚拟机有时候ping不通主机
- 控制面板\网络和 Internet\网络和共享中心\高级共享设置--启用文件和打印机共享，虚拟机即可ping通主机
- 如果还出现ping不通的情况检查下面的参数肯定写错了，检查不出来就删了重写
- 发现数据库还是连接不上, 或者其他服务redis等通过 telnet ip port 来测试端口访问, 访问失败则关闭防火墙
- 防火墙状态变化可能会引起docker自定义链有问题, 最好重启下docker(docker启动image错误)
- 如果发现ip地址互相可以ping通, 但是端口不通先试试getenforce与setenforce 0命令, 还不行就重启linux系统
```
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.0.109
NETMASK=255.255.255.0
GATEWAY=192.168.0.1
DNS1=8.8.8.8
```

#### 集群前置条件
- 关闭防火墙; 关闭安全策略; 关闭内存交换; 指定主机名
```
systemctl status firewalld.service
systemctl disable firewalld

sed -i 's/enforcing/disabled/' /etc/selinux/config
setenforce 0

sed -ri 's/.*swap.*/#&/' /etc/fstab
swapoff -a
```
- 添加主机与ip的对应关系, 类似windows域名与ip绑定
```
# 设置主机名
hostnamectl set-hostname <newhostname>
# 添加主机名与 IP 对应关系
vi /etc/hosts
192.168.0.107 k8s-node1
192.168.0.108 k8s-node2
192.168.0.109 k8s-node3
```
- 将桥接的IPv4流量传递到iptables的链
```
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
# 应用该规则
sysctl --system
```

#### 应用介绍
- kubeadm: 是用来快速部署kubernetes集群的工具, 部署成功使命结束
- kubectl: 命令行工具管理Kubernetes集群, 部署好了用该命令进行交互
- kubelet:一个在集群中每个节点上运行的代理, 它保证容器都运行在Pod中; 负责维护容器的生命周期, 同时也负责Volume(CSI)和网络(CNI)的管理;

#### 所有节点安装docker, kubeadm, kubelet, kubectl
- 文档中配置docker命令修改; docker镜像加速用自己的
```
# docker配置镜像源
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# 安装k8s组件
yum install -y kubelet-1.17.3 kubeadm-1.17.3 kubectl-1.17.3
```

#### 根据文档部署K8S集群
- 初始化主节点命令
```
kubeadm init --apiserver-advertise-address=192.168.0.107 --image-repository registry.cn-hangzhou.aliyuncs.com/google_containers --kubernetes-version v1.17.3 --service-cidr=10.96.0.0/16 --pod-network-cidr=10.244.0.0/16
```
- 从节点加入主节点命令
```
kubeadm join 192.168.0.107:6443 --token v5bld3.xx70vflfpr1nzxd6 --discovery-token-ca-cert-hash sha256:647541701bbbe6d7d50e3ed3634db3da0c834d
```

#### 常用命令
- `kubectl get`: 可以获取nodes; pods; services; deployments; 等信息
- `kubectl apply`: 以文件或标准输入为准应用或更新资源, 配置pods; services; deployments;等信息

#### KubeSphere可视化
- 默认的dashboard没啥用, 我们用kubesphere可以打通全部的devops链路
- 安装参考文档两篇: 在Kubernetes上最小化安装KubeSphere; 启用可插拔组件;
- 主要功能: 建立多租户系统; 创建DevOps工程; 创建并部署WordPress;(快速入门文档)

#### K8S与Zookeeper
- k8s只是将应用部署很多份, 这些应用之间是并没有领导者与跟随者, 相对独立未能形成集群
- Zookeeper是进行分布式协调的工具, 将多份应用管理成为一个集群, 以集群方式对外提供服务