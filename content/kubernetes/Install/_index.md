+++
title = "Install"
weight = 1
+++



## Kubernetes install  

{{% notice warning %}}
**문서 참고 사항**  
작성중인 문서 입니다.  
단순 참고용으로 부탁 드립니다.  
{{% /notice %}}

{{% notice tip %}}
**참고 사이트**  
[Kubernetes 사이트](https://kubernetes.io/docs/setup/cri/)  
{{% /notice %}}


{{% notice info %}}
**Config 참고사항**  
k8s Kubernetes 설치시 모든 node (master 를 포함한 모든노드) , 또는 master node 에서만 작업을 해야 합니다.  
{{% /notice %}}


> 1) hosts 파일 설정  
~~~
모든 노드에서 작업
# vi /etc/hosts

10.10.10.27     k8s-master
10.10.10.28     k8s-node01
10.10.10.29     k8s-node02
~~~

> 2) selinux disable  

~~~
모든 노드에서 작업
# vi /etc/selinux/config
 
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled
~~~


> 3) sysctl.conf 설정  

~~~
모든 노드에서 작업 
# cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF


# sysctl --system
* Applying /usr/lib/sysctl.d/00-system.conf ...
* Applying /usr/lib/sysctl.d/10-default-yama-scope.conf ...
kernel.yama.ptrace_scope = 0
* Applying /usr/lib/sysctl.d/50-default.conf ...
kernel.sysrq = 16
kernel.core_uses_pid = 1
net.ipv4.conf.default.rp_filter = 1
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.accept_source_route = 0
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.promote_secondaries = 1
net.ipv4.conf.all.promote_secondaries = 1
fs.protected_hardlinks = 1
fs.protected_symlinks = 1
* Applying /etc/sysctl.d/99-sysctl.conf ...
* Applying /etc/sysctl.d/k8s.conf ...
* Applying /etc/sysctl.conf ...

~~~

> 4) swapoff (k8s 의 경우 swap 을 사용하지 않습니다.)  
~~~
모든 노드에서 작업 
# swapoff -a
 
# vi /etc/fstab
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=d7bb5d3b-5b37-47e0-8c26-fe40f7311597 /                       xfs     defaults        0 0
UUID=43ec35ea-2e35-46f1-864c-b13603a8acac /boot                   xfs     defaults        0 0
#UUID=2de336ec-4a33-36r1-8w2s-asdf2342ccgg swap                   swap     defaults        0 0

~~~


> 5) docker-ce 설치  

~~~
모든 노드에서 작업
# yum install -y yum-utils device-mapper-persistent-data lvm2
# yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# yum install -y docker-ce
 
# mkdir /etc/docker
# mkdir /etc/docker
 
# cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
EOF
 
 
# mkdir -p /etc/systemd/system/docker.service.d
# systemctl daemon-reload
# systemctl restart docker
~~~




> 6) kubernetes 설치 및 system 리부팅  
~~~
# vi /etc/yum.repos.d/kubernetes.repo

[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
        https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
 
# yum install -y kubelet kubeadm kubectl
# init 6
 
# systemctl start docker ; systemctl enable docker
# systemctl start kubelet ; systemctl enable kubelet
~~~


{{% notice note %}}
**k8s-master Only**  
k8s-master node 에서만 작업 합니다.  
{{% /notice %}}

> 7) k8s-master node 에서 apiserver 와 pod-network 를 설정 합니다.  

~~~
[root@k8s-master ~]# kubeadm init --apiserver-advertise-address=10.10.10.27 --pod-network-cidr=20.20.0.0/16
~중략
 
[addons] Applied essential addon: kube-proxy
 
Your Kubernetes control-plane has initialized successfully!
 
To start using your cluster, you need to run the following as a regular user:
 
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
 
You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/
 
Then you can join any number of worker nodes by running the following on each as root:
 
kubeadm join 10.10.10.27:6443 --token syojz8.svxybs8x0f3iy28a \
    --discovery-token-ca-cert-hash sha256:b28c6474e92e2bc87e8f7b470119e506df36ae6ae08a8f50dd070f5d714a28e1
[root@k8s-master ~]#
 
[root@k8s-master ~]# mkdir -p $HOME/.kube
[root@k8s-master ~]# sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
[root@k8s-master ~]# sudo chown $(id -u):$(id -g) $HOME/.kube/config
[root@k8s-master ~]# kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
 
[root@k8s-master ~]# kubectl get nodes
NAME         STATUS   ROLES    AGE     VERSION
k8s-master   Ready    master   2m22s   v1.14.1
[root@k8s-master ~]# kubectl get pods --all-namespaces
NAMESPACE     NAME                                 READY   STATUS    RESTARTS   AGE
kube-system   coredns-fb8b8dccf-kwnbc              0/1     Running   0          112s
kube-system   coredns-fb8b8dccf-nlpqx              0/1     Running   0          112s
kube-system   etcd-k8s-master                      1/1     Running   0          86s
kube-system   kube-apiserver-k8s-master            1/1     Running   0          79s
kube-system   kube-controller-manager-k8s-master   1/1     Running   0          85s
kube-system   kube-flannel-ds-amd64-5rft8          1/1     Running   0          32s
kube-system   kube-proxy-fvf6z                     1/1     Running   0          112s
kube-system   kube-scheduler-k8s-master            1/1     Running   0          82s
[root@k8s-master ~]#
~~~


> 8) k8s-node01 / k8s-node02 에서 작업  

~~~
[root@k8s-node01 ~]# kubeadm join 10.10.10.27:6443 --token syojz8.svxybs8x0f3iy28a \
    --discovery-token-ca-cert-hash sha256:b28c6474e92e2bc87e8f7b470119e506df36ae6ae08a8f50dd070f5d714a28e1
 
[root@k8s-node02 ~]# kubeadm join 10.10.10.27:6443 --token syojz8.svxybs8x0f3iy28a \
    --discovery-token-ca-cert-hash sha256:b28c6474e92e2bc87e8f7b470119e506df36ae6ae08a8f50dd070f5d714a28e1
~~~

> 9) k9s-master 에서 확인  
~~~
[root@k8s-master ~]# kubectl get nodes
NAME         STATUS     ROLES    AGE     VERSION
k8s-master   Ready      master   4m32s   v1.14.1
k8s-node01   Ready      <none>   79s     v1.14.1
k8s-node02   NotReady   <none>   17s     v1.14.1
[root@k8s-master ~]#
~~~


> 10) k8s Testing  (k8s-master 에서만 진행 합니다.)

~~~
[root@k8s-master ~]# kubectl create deployment nginx --image=nginx
[root@k8s-master ~]# kubectl describe deployment nginx
Name:                   nginx
Namespace:              default
CreationTimestamp:      Fri, 03 May 2019 00:28:11 +0900
Labels:                 app=nginx
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx
Replicas:               1 desired | 1 updated | 1 total | 0 available | 1 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:        nginx
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      False   MinimumReplicasUnavailable
  Progressing    True    ReplicaSetUpdated
OldReplicaSets:  <none>
NewReplicaSet:   nginx-65f88748fd (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  18s   deployment-controller  Scaled up replica set nginx-65f88748fd to 1
[root@k8s-master ~]#
 
 
[root@k8s-master ~]# kubectl create service nodeport nginx --tcp=80:80
 
 
[root@k8s-master ~]# kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        6m33s
nginx        NodePort    10.102.109.228   <none>        80:30187/TCP   21s
~~~

> 11) k8s-master node 에서 nginx 확인  

~~~
[root@k8s-master ~]#  curl k8s-node01:30187
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>
 
<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>
 
<p><em>Thank you for using nginx.</em></p>
</body>
</html>
[root@k8s-master ~]#
~~~

> 12) pods scale up  (k8s-master 에서 진행)  
~~~
최초 생성시 1개의 pods 입니다. 
 
[root@k8s-master ~]# kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-65f88748fd-8lqrb   1/1     Running   0          5m12s
[root@k8s-master ~]#
 
pods 을 5개로 늘립니다. 
[root@k8s-master ~]# kubectl scale --replicas=5 deployment/nginx
deployment.extensions/nginx scaled
 
 
[root@k8s-master ~]# kubectl get pods
NAME                     READY   STATUS              RESTARTS   AGE
nginx-65f88748fd-6v7n5   1/1     Running             0          13s
nginx-65f88748fd-86svl   0/1     ContainerCreating   0          13s
nginx-65f88748fd-8lqrb   1/1     Running             0          12m
nginx-65f88748fd-pq8p8   0/1     ContainerCreating   0          13s
nginx-65f88748fd-w4tq8   0/1     ContainerCreating   0          13s
[root@k8s-master ~]#
~~~
