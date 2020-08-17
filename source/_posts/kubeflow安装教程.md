---
title: kubeflow安装教程
date: 2020-08-17 15:11:06
categories:
- 编程学习
tags:
- kubeflow
- 教程
---
# 1.安装docker
```
#关闭交换空间
sudo swapoff -a
#安装docker 版本不影响，任意版本docker即可
# 更新软件源
sudo apt-get update
# 安装所需依赖
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
# 安装 GPG 证书
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# 新增软件源信息
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
# 再次更新软件源
sudo apt-get -y update
# 安装 Docker CE 版
sudo apt-get -y install docker-ce
```
<!-- more -->
##1.2验证docker
```
docker version
```

##1.2配置加速器
```
nano /etc/docker/daemon.json
```

在其中加入镜像内容如下
```
{
  "registry-mirrors": [
    "https://registry.docker-cn.com"
  ]
}
```

##1.3保存文件，并重启docker
```
sudo systemctl restart docker

docker info
...
# 出现如下语句即表示配置成功
Registry Mirrors:
 https://registry.docker-cn.com/
...

#修改 Hostname
hostnamectl set-hostname kubernetes-master

#验证,看hostname是否改变
hostnamectl
```

#2.安装kubeadm
```
apt-get update && apt-get install -y apt-transport-https
# 安装 GPG 证书
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add -
# 写入软件源；注意：我们用系统代号为 bionic，但目前阿里云不支持，所以沿用 16.04 的 xenial
cat << EOF >/etc/apt/sources.list.d/kubernetes.list
>deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
>EOF

# 安装
apt-get update  
#必须指定版本
apt-get install -y kubelet=1.14.1-00 kubeadm=1.14.1-00 kubectl=1.14.1-00  
#kubeadm 重启
kubeadm reset
# 设置 kubelet 自启动，并启动 kubelet
systemctl enable kubelet && systemctl start kubelet
# 导出配置文件
kubeadm config print init-defaults --kubeconfig ClusterConfiguration > kubeadm.yml
```

##2.2修改文件 kubeadm.yml
```
nano kubeadm.yml
```

修改配置为如下内容
```
apiVersion: kubeadm.k8s.io/v1beta1
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: abcdef.0123456789abcdef
  ttl: 24h0m0s
  usages:
  - signing
  - authentication
kind: InitConfiguration
localAPIEndpoint:
  # 修改为主节点内网 IP
  advertiseAddress: 172.17.166.73
  bindPort: 6443
nodeRegistration:
  criSocket: /var/run/dockershim.sock
  name: kubernetes-master
  taints:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
---
apiServer:
  timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta1
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controlPlaneEndpoint: ""
controllerManager: {}
dns:
  type: CoreDNS
etcd:
  local:
    dataDir: /var/lib/etcd
# 国内不能访问 Google，修改为阿里云
imageRepository: registry.aliyuncs.com/google_containers
kind: ClusterConfiguration
# 修改版本号
kubernetesVersion: v1.14.1
networking:
  dnsDomain: cluster.local
  # 配置成 Calico 的默认网段
  podSubnet: "192.168.0.0/16"
  serviceSubnet: 10.96.0.0/12
scheduler: {}
---
# 开启 IPVS 模式
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
featureGates:
  SupportIPVSProxyMode: true
mode: ipvs
```





##2.3kubeadm 初始化
```
kubeadm init --config=kubeadm.yml | tee kubeadm-init.log
```
出现如下情况表示成功
```
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:
```
##2.4配置kubectl
```
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
# 非 ROOT 用户执行
chown $(id -u):$(id -g) $HOME/.kube/config
```
查看node状态
```
kubectl get nodes
# 能够打印出节点信息即表示成功
NAME                STATUS     ROLES    AGE     VERSION
kubernetes-master   NotReady   master   8m40s   v1.14.1
```
##2.5配置网络 
```
#cni 这里3.7版本须与之前的kubeadm1.14.1对应，否则会出错
kubectl apply -f https://docs.projectcalico.org/v3.7/manifests/calico.yaml
#确认安装是否成功
watch kubectl get pods --all-namespaces
等待几分钟，到所有的pod都是running时继续下一步
```
##2.6修改master节点状态，能让master部署应用
```
#查看node状态
kubectl describe node kubernetes-master
#将master当node用
kubectl taint node kubernetes-master node-role.kubernetes.io/master-
```


#3拉取kubeflow镜像
```
#拉取脚本
git clone https://github.com/shikanon/kubeflow-manifests.git
#创建命名空间
kubectl create namespace kubeflow
#完成后
cd kubeflow-manifests
#生成yml
python run.py
#启动
cd yaml
kubectl apply -f .
cd ..
cd local-path
kubectl apply -f local-path-storage.yaml

#查看结果
kubectl get pod -nkubeflow
等待所有pod 都为running
```
#连通外网
```
创建一个新的screen ,screen 保证在退出远程连接之后程序依然能运行
screen -R kubeflow
sudo kubectl port-forward -n istio-system svc/istio-ingressgateway 8080:80 --address 0.0.0.0
```

#kubeadm 重新启动
如果出现有的pod是ImagePullBackOff或者CrashLoopBackOff，重启整个服务
```
rm -r $HOME/.kube
kubectl reset
#重新从2.3kubeadm 初始化执行一遍
```




看日志
>journalctl -f -u kubelet

看node
>kubectl get nodes

#拉取jupyter镜像
>docker pull andreyneverov/tensorflow-1.13.1-notebook-cpu:v0.5.0
#将镜像改为kubeflow需要的名字
>docker tag andreyneverov/tensorflow-1.13.1-notebook-cpu:v0.5.0 gcr.io/kubeflow-images-public/tensorflow-1.13.1-notebook-cpu:v0.5.0


registry.cn-shenzhen.aliyuncs.com/shikanon/kubeflow-images-public.katib.v1alpha2.metrics-collector:v0.1.2-alpha-289-g14dad8b

kubectl -nkubeflow describe experiment random-example

docker tag katib/metrics-collector:latest registry.cn-shenzhen.aliyuncs.com/shikanon/kubeflow-images-public.katib.v1alpha2.metrics-collector:v0.1.2-alpha-289-g14dad8b