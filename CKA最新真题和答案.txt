
---
https://blog.csdn.net/u011127242/category_10823035.html
---
CKA2021最新真题解析01--权限控制RBAC         (4)
CKA2021最新真题解析02--设置节点不可用       (4)
CKA2021最新真题解析03--升级 kubeadm         (7)
CKA2021最新真题解析04--备份还原 etcd         (7)
CKA2021最新真题解析05--配置网络策略 NetworkPolicy (4)
CKA2021最新真题解析06--创建 service          (4)
CKA2021最新真题解析07--按要求创建 Ingress 资源 (7)
CKA2021最新真题解析08--扩容 deployment       (4)
CKA2021最新真题解析09--调度 pod 到指定node    (4)
CKA2021最新真题解析10--统计 ready 状态节点数量 (4)
CKA2021最新真题解析11--创建多容器的 pod        (4)
CKA2021最新真题解析12--按要求创建 PV           (4)
CKA2021最新真题解析13--按要求创建 PVC          (7)
CKA2021最新真题解析14--监控 pod 的日志            (5)
CKA2021最新真题解析15--添加 sidecar 容器并输出日志   (7)   #######
CKA2021最新真题解析16--查看 cpu 使用率最高的 pod     (5)
CKA2021最新真题解析17--排查集群中故障节点           (13)

===
L*** CKA2021最新真题解析01--权限控制RBAC > Role-based access control (RBAC) 
===
##################
clusterrole没有namespace, 参数是--verb，--resource
serviceaccount参数只有--namespace
rolebinding的名字使用serviceaccount的名字，参数有--namespace,--clusterrole --serviceaccount.
##################



create a new ClusterRole, bind it to a specific ServiceAccount. limited to the namespace.
---
user1@k8s-master:~/cka-lab-init/1$ vim ns-app-team1.yaml 
apiVersion: v1
kind: Namespace
metadata:
  name: app-team1
user1@k8s-master:~/cka-lab-init/1$ kubectl apply -f ns-app-team1.yaml 
namespace/app-team1 created
user1@k8s-master:~/cka-lab-init/1$ kubectl get namespaces app-team1 
NAME        STATUS   AGE
app-team1   Active   17s

---
>kubectl config use-context k8s
>kubectl create ns app-team1  (kubectl get ns)
>kubectl create serviceaccount cicd-token -n app-team1
>kubectl create clusterrole deployment-clusterrole --verb=create --resource=deployement,statefulset,daemonset
(kubectl get clusterrole)
>kubectl -n app-team1 create rolebinding cicd-token --clusterrole=deployment-clusterrole --serviceaccount=app-team1:cicd-token

---
(bbs)
用kubectl config use-context k8s。

创建 名字为deployment-cluserrole的ClusterRole，可以创建Deployment，StatefulSet，DaemonSet。
在namespace名字为app-team1下创建名字为cicd-token的ServiceAccount。
将创建好的ClusterRole和cicd-token进行绑定。
答案：

切换到kubectl config use-context k8s。

kubectl create clusterrole deployment-clusterrole --verb=create --resource=Deployment，StatefulSet，DaemonSet
Kubectl create serviceaccourt cicd-token -n app-team1
Kubectl create rolobinding cicd-token --clusterrole=deployment-clusterrole --serviceaccount=app-team1:cicd-token --namespace=app-team1
---


===
L*** CKA2021最新真题解析02--设置节点不可用
===
#############################
不需要登陆master节点.而直接使用cordon和drain
drain的参数有--ignore-daemonsets, --force,而没有--delete-local-data 
#############################
---

>kubectl config use-context k8s    (kubectl config view)
>kubectl cordon ek8s-node-1
(Mark node as unschedulable.)
>kubectl drain ek8s-node-1 --ignore-daemonsets --delete-local-data --force
(Drain node in preparation for maintenance) (参数 --delete-local-data  1.21 版本的命令为 --delete-emptydir-data, 1.19 版本的为 --delete-local-data)

---
(bbs)
用kubectl config use-context ek8s
名字为ek8s-node-0的节点，设置为不可用
答案：

切换到kubectl config use-context ek8s
kubectl drain ek8s-node-0 --ignore-daemonsets --force
验证：
kubectl get nodes <node name>


---
===
L*** CKA2021最新真题解析03--升级 kubeadm 
===
(https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)
#################################
kubeadm=1.21.1-00 
Kubeadm upgrade apply v1.21.1 --etcd-upgrade=false
#################################

kubectl get nodes (查看版本)
ssh mk8s-master-0 (登陆master node)
kubectl cordon mk8s-master-0   (cordon vt. 用警戒线围住)
kubectl drain mk8s-master-0 --ignore-daemonsets
apt-cache show kubeadm |grep -I <target version>
(apt-cache 命令可显示 APT 内部数据库里的多种信息。这些信息是从 sources.list 文件内聚集不同来源的缓存。于运行 apt update 运作时产生的)
apt-mark unhold kubeadm kubectl kubelet 
(hold –标记指定软件包为保留(held back)，阻止软件自动更新)
(unhold –取消指定软件包的保留(held back)标记，解除阻止自动更新) 
(apt-get update && )apt-get install -y kubeadm=1.20.1-00 kubelet=1.20.1-00 kubectl=1.20.1-00
apt-mark hold kubeadm kubectl kubelet
(kubeadm version)
(kubelet --version)
(kubectl version)
kubeadm upgrade plan (plan是check upgrade可行不可行，可能的target 版本)
kubeadm upgrade apply v1.20.1 --etcd-upgrade=false
// kubectl rollout undo deployment coredns -n kube-system ,有些大佬建议rollout coredns，笔者考试的时候没有rollover
kubectl uncordon mk8s-master-0

Restart the kubelet:
sudo systemctl daemon-reload
sudo systemctl restart kubelet

---
eccd@director-0-jpyosb5gcccd-c1-nf01-01:~> kubectl get nodes
NAME                                            STATUS   ROLES                  AGE    VERSION
master-0-jpyosb5gcccd-c1-nf01-01                Ready    control-plane,master   184d   v1.20.4
master-1-jpyosb5gcccd-c1-nf01-01                Ready    control-plane,master   184d   v1.20.4
master-2-jpyosb5gcccd-c1-nf01-01                Ready    control-plane,master   184d   v1.20.4
worker-pool1-1un8j4q7-jpyosb5gcccd-c1-nf01-01   Ready    worker                 184d   v1.20.4
worker-pool1-5mcc039y-jpyosb5gcccd-c1-nf01-01   Ready    worker                 184d   v1.20.4
worker-pool1-bh73e4y2-jpyosb5gcccd-c1-nf01-01   Ready    worker                 184d   v1.20.4
worker-pool1-t1m5ut8i-jpyosb5gcccd-c1-nf01-01   Ready    worker                 184d   v1.20.4
eccd@director-0-jpyosb5gcccd-c1-nf01-01:~> 
---
(bbs)
用kubectl config use-context mk8s

Master节点使用Kubeadm将升级至1.21.1。升级前master设置drain，升级后回复uncordon
Kubelet和kubectl同样升级，但是etcd不升级
答案：

切换到kubectl config use-context mk8s

kubectl get nodes(确定哪个node是master )
kubectl drain ek8s-node-0--ignore-daemonsets --force
ssh master节点，并sudo -i 提权
apt-get install -y kubeadm=1.21.1-00
Kubeadm upgrade plan (验证)
Kubeadm upgrade apply v1.21.1 --etcd-upgrade=false
Apt-get install -y kubelet=1.21.1-00 kubectl=1.21.1-00
systemctl daemon-reload
systemctl restart kubelet
Exit(返回到mk8s)
kubectl uncordon ek8s-node-0
验证：

Kubectl descript ek8s-node-0（查看状态是否非unschedule）
---
(总结)
切换到kubectl config use-context mk8s
kubectl get nodes(确定哪个node是master )
kubectl cordon mk8s-master-0   (cordon vt. 用警戒线围住)
kubectl drain ek8s-node-0--ignore-daemonsets --force
ssh master节点，并sudo -i 提权
kubeadm version
kubectl version
kubelet --version

apt-mark unhold kubeadm kubectl kubelet

apt-cache show kubeadm  可能变成了apt-cache madison kubeadm
apt-get install -y kubeadm=1.21.1-00
Kubeadm upgrade plan (验证)
Kubeadm upgrade apply v1.21.1 --etcd-upgrade=false
Apt-get install -y kubelet=1.21.1-00 kubectl=1.21.1-00


apt-mark hold kubeadm kubectl kubelet
systemctl daemon-reload
systemctl restart kubelet

kubeadm version
kubectl version
kubelet --version

Exit(返回到mk8s)
kubectl uncordon ek8s-node-0
验证：

Kubectl describe ek8s-node-0（查看状态是否非unschedule）


---

===
L*** CKA2021最新真题解析04-备份还原 etcd
===
#######################################
在命令前要有ETCDCTL_API=3
使用命令是 etcdctl snapshot save/restore, 用的参数有--endpoints, --cacert, --cert, --key. 最后直接加file.
#######################################

1 练习目标
备份 https://127.0.0.1:2379 上的 etcd 数据到 /var/lib/backup/etcd-snapshot.db
使用之前的文件 /data/backup/etcd-snapshot-previous.db 还原 etcd
使用指定的 ca.crt 、 etcd-client.crt 、etcd-client.key
---
备份：
ETCDCTL_API=3 (指定ETCD版本为3)
etcdctl --endpoints=https://172.0.0.1:2379 --cacert=‘/opt/xxx/ca.crt’ --cert=‘/opt/xxx/etcd-client.crt’ --key=‘/opt/xxx/etcd-client.key’ snapshot save /var/lib/backup/etcd-snapshot.db
etcdctl --endpoints=https://172.0.0.1:2379 --cacert=‘/opt/xxx/ca.crt’ --cert=‘/opt/xxx/etcd-client.crt’ --key=‘/opt/xxx/etcd-client.key’ snapshot status /var/lib/backup/etcd-snapshot.db -wtable

还原：
ETCDCTL_API=3 
etcdctl --endpoints=https://172.0.0.1:2379 --cacert=/opt/xxx/ca.crt --cert=/opt/xxx/etcd-client.crt --key=/opt/xxx/etcd-client.key snapshot restore /var/lib/backup/etcd-snapshot-previous.db

还原成功后，最好通过 get nodes 确定集群状态是正常的
注意1：还原这里使用 snapshot restore，之前手误写成 restore save，经读者提醒已经更新
注意2：笔者和一位考98分的群友沟通，他反馈还原这里存在一点问题(虽然笔者还原的时候没有报错),以下是他的思路(笔者后续会抽空测试下，然后重新整理下还原的参考方法)，建议最新读者自己学习时候尝试下这个思路
1. restore的时候会在执行目录生成一个 default.etcd 的文件夹，里面是etcd的数据文件。
2. 然后切换到root， systemctl cat etcd 看一下启动的时候的参数，etcd 的数据目录在哪里。 
3. 然后 stop etcd， 将之前的数据备份然后 mv default.etcd, 到配置文件指定的目录，给上属组和主 都是etcd。然后启动etcd就行了
---
etcd还原提供一个思路，经测试可行
建议使用以下方式还原，适用于kubeadm方式部署的还原，二进制部署的话，仅供参考
#1.首先先将etcd、api停止了，移动下面的文件后，过了一会容器会自动停止
mv /etc/kubernetes/manifests /etc/kubernetes/manifests.bak
#2.然后也备份一下原来etcd的文件夹
mv /var/lib/etcd /var/lib/etcd.bak
#3.恢复数据，会在当前目录生成一下 default.etcd 目录
ETCDCTL_API=3  etcdctl snapshot restore  /data/backup/etcd-snapshot.db
#4.然后将 default.etcd 目录拷贝到原先目录下，并重命名为 etcd
mv default.etcd /var/lib/etcd
#5.启动etcd，api docker容器，把最开始移动的文件夹移回来过一会就可以启动了
mv /etc/kubernetes/manifests.bak /etc/kubernetes/manifests
#6.测试
kubectl get nodes
kubectl get pods
# 能看到你之前的pods与nodes数据那就没什么问题了
---
(bbs)
答案：

ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
  --cacert=/opt/KUIN00601/ca.crt --cert= --key=/opt/KUIN00601/etcd-client.crt \ --key=/opt/KUIN00601/etcd-client.key  snapshot save /data/backup/etcd-snapshot.db

ETCDCTL_API=3 etcdctl --endpoints 127.0.0.1:2379 snapshot restore /srv/data/etcd-snapshot-previous.db

---


===
L*** CKA2021最新真题解析05-配置网络策略 NetworkPolicy
===
#######################################
my-app 这个 namespace 要注意标签是什么,可能需要加标签
{}中间没有空格， {}是在matchLabels后面,但也可以在podSelector后面。

#######################################

(https://kubernetes.io/docs/concepts/services-networking/network-policies/)
题目概述
在命名空间 fubar 中创建网络策略 allow-port-from-namespace
只允许 ns my-app 中的 pod 连上 fubar 中 pod 的 80 端口
--
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
  namespace: fubar
spec:
  podSelector:
    matchLabels: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          my-app-key: my-app-value
      podSelector:  # 本行和下行可以去掉。好像不去掉也可以，不去掉说明是上面的namespace下的所有pod.如果加上-，就是没有上面namespace的控制。
        matchLabels: {}
    ports:
    - protocol: TCP
      port: 80

注意： 
2月份之前的真题 网络策略和来源的pod 都是同一个ns，这次调整来源为 my-app，而不是所有 ns的 pod 都能访问 fubar下 pod的80 端口；
因此需要 查看my-app的labels，然后在 namespaceSelector 中添加对应的labels；很多考过的小伙伴反应实际考试的时候没有label，因此建议实际考试的时候加一个label；
kubectl label namespaces my-app name=my-app
由于之前刷题都是同一个ns，笔者考试的时候也忽略了这里有2个ns，这应该是笔者踩的一个坑。
--

--
>vim 5.yaml
>kubectl apply -f 5.yaml
>kubectl get networkpolicies.networking.k8s.io 

>kubectl get namespaces <namespace name> -o wide --show-labels 
>kubectl label namespaces etcd  <key>=<value>

--
(bbs)
换到kubectl config use-context hk8s。

Kubectl get ns --show-labels(查看internal的标签)
Vim NetworkPolicy.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
  namespace: fubar
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          label1: internal
    ports:
    - protocol: TCP
      port: 80

kubectl apply -f NetworkPolicy.yaml
---

===
L*** CKA2021最新真题解析06-- 重配deployment & 创建 service
===
#######################################
用kubectl edit 命令 在container 下面的 containers name下面加ports.有3个参数containerPort， name和protocol(TCP).
用kubectl edit 命令以后还需要使用kubectl apply么？现在看是不需要~
用kubectl expose命令建 service, 参数有--name, --type, --port --target-port
#######################################
(https://kubernetes.io/docs/concepts/services-networking/service/)
题目概述
重新配置已有的 deployment front-end，添加一个名称为 http 的端口，暴露80/TCP
创建名称为 front-end-svc 的 service，暴露容器的 http 端口
配置service 的类别为NodePort.

---
0) 创建测试环境
# vim nginx.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: front-end
  name: front-end
spec:
  replicas: 1
  selector:
    matchLabels:
      app: front-end
  template:
    metadata:
      labels:
        app: front-end
    spec:
      containers:
      - image: nginx:1.21
        name: nginx
        ports:									(新加的内容)
        - containerPort: 80
          name: http
          protocol: TCP
# kubectl apply -f nginx.yaml 

1）edit front-end ，在containers 中添加如下内容
kubectl edit deployment front-end
在container的name下面加如下内容！
ports:
- name: http
  protocol: TCP
  containerPort: 80

2）expose 对应端口
kubectl expose deployment front-end --type=NodePort --port=80 --target-port=http --name=front-end-svc

---
(bbs)
用kubectl config use-context k8s。

通过一个已经存在的名字为front-end的deployment增加一个特殊的配置：容器为nginx，名字为http端口为80。
通过修改后front-end这个deployment导出一个名字为front-end-svc，并且该SVC可以启用新修改的配置。
答案：

切换到kubectl config use-context k8s。

kubectl get deployment.app front-end -o yaml（查看front-end，与后面无关）
kubectl edit deployment.app front-end
在name=nginx下面插入
   ports:
     - name: http
       containerPort: 80

kubectl describe deployment.app front-end(看到已经增加的http和80端口，则代表更改完成)
kubectl expose deployment.app frond-end --type=NodePort --port=80 --target-port=http --name=front-end-svc

验证
Kubectl get svc(看到有front-end-svc)
Kubectl describe svc front-end-svc (看到http和80端口的映射状态，代表成功)
---



===
L*** CKA2021最新真题解析07-按要求创建 Ingress 资源
===
#######################################
基于yaml,在name下面加上namespace , 但internal IP就是不出来~~
视频讲ip在ingress yaml文件的最后~
#######################################

(https://kubernetes.io/docs/concepts/services-networking/ingress/)

创建一个新的 Ingress 资源，名称 ping，命名空间 ing-internal
使用 /hello 路径暴露服务 hello 的 5678 端口

解析：
拷贝官文的 yaml 案例，修改相关参数即可
设置成功后需要通过 curl -kl <INTERNAL_IP>/hello 来测试

---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ping
  namespace: ing-internal  (要基于yaml加这个namespace)
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /hello
        pathType: Prefix
        backend:
          service:
            name: hello
            port:
              number: 5678
创建成功后， 通过get ingress 查看ingress的内外IP，然后通过提供的 curl 测试 ingress 是否正确，正常情况下是会输出 hello 的
---
>kubectl get ingress
---
(bbs)
用kubectl config use-context k8s。
创建名字为pong的ingress，服务名hi路径/hi，服务端口5678。
答案：
切换到kubectl config use-context k8s。

Vim ingress,yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  namespace: ing-internal
spec:
  rules:
  - http:
      paths:
      - path: /hi
        pathType: Prefix
        backend:
          service:
            name: hi
            port:
              number: 5678

验证

Curl -kL <internal_ip>/hi
---



===
L*** CKA2021最新真题解析08- 扩容 deployment
===
#######################################
kubectl scale 没有额外的参数,只有--replicas 
#######################################
(https://kubernetes.io/docs/tutorials/kubernetes-basics/scale/scale-intro/)
扩容 deployment guestbook 为 6个pod

---
kubectl scale deployment --replicas=6 guestbook

---
(bbs)
用kubectl config use-context k8s。
将guestbook的deployment内pod数量更改为6。
答案：
切换到kubectl config use-context k8s。
Kubectl scale deployment guestbook --replicas=6
验证
Kubectl get deployment guestbook


===
L*** CKA2021最新真题解析09-调度 pod 到指定node
===
#######################################
yaml文件格式：开头每级缩进两个空格，:和-后面是一个空格，行尾没有空格~
容器image和name是一样的
#######################################
(https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)
创建 pod 名称 nginx-kusc0041，镜像 nginx
调度该 pod 到 disk=ssd 的节点上
解析：
拷贝官文案例，修改下 pod 名称和镜像，删除多余的部分即可，又是个送分题
---
切换 context 

apiVersion: v1
kind: Pod
metadata:
  name: nginx-kusc0041
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    disk: ssd
---

>kubectl label nodes <node-name> <label-key>=<label-value>


> kubectl run nginx-kusc0041 --image=nginx --dry-run=client -oyaml > 9.yaml

>kubectl apply -f 9.yaml
>kubectl get pod -o wide

---
(bbs)
OK
---





===
L*** CKA2021最新真题解析10-统计 ready 状态节点数量
===
###################################
node不区分namespace， NoSchedule 的信息在 describe 中显示
###################################
(https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)
统计 ready 状态节点
要求不包括 NoSchedule 的节点

---
> kubectl describe node | grep -i taint
> echo 2 > /opt/xxxxxx
---
(bbs)
切换 context
kubectl get nodes
kubectl describe nodes | grep -i taint | grep NoSchedule
两者数据相减，echo number > /path/file
---

===
L*** CKA2021最新真题解析11-创建多容器的 pod
===
###################################
参考的还是pod的yaml，每一个name前面都是 '- name'
###################################

(https://kubernetes.io/docs/concepts/workloads/pods/)
题目概述
创建名称为 kucc1 的 pod
pod 中运行 nginx 和 redis 两个示例

解析：
dry-run 一个pod，多追加一个镜像即可，又是一送分题
---
apiVersion: v1
kind: Pod
metadata:
  name: kucc1
spec:
  containers:
  - name: nginx
    image: nginx
  - name: redis
    image: redis
---
(bbs)

kubectl config use-context k8s。

创建一个名字为kucc8的pod，里面包含2个容器redis和consul。
答案：

Vim kucc8.yaml
apiVersion: v1
kind: Pod
metadata:
  name: kucc8
spec:
  containers:
  - name: redis
    image: redis
  - name: consul
    image: consul

---
Kubectl apply -f kucc8.yaml
Kubectl get pod
---
 

===
L*** CKA2021最新真题解析12-按要求创建 PV
===
###################################
用yaml.
###################################
(https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
创建一个名为 app-config 的PV，PV的容量为2Gi，访问模式为 ReadWriteMany，volume 的类型为 hostPath
pv 映射的 hostPath 为 /srv/app-config 目录
---
切换 context

apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-config
spec:
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: /srv/app-config
---
(bbs)
任务：

使用kubectl config use-context hk8s。
创建一个名字为app-config，大小2G，redawritenany，类型为host path，path路径为/srv/app-config的卷。
答案：
使用kubectl config use-context hk8s。
Vim app-config.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-config
spec:
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: /srv/app-config
Kubectl apply -f app-config.yaml
Kubectl get pv
---

===
L*** CKA2021最新真题解析13-按要求创建 PVC
===
###################################
yaml页面使用的是与PV前一题同一个.
创建PVC时，class就是storageClassName。
创建pod时，volume的名字使用的是mypv.
这题Kubectl edit 后面要加--record
###################################
(https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
使用指定 storageclass csi-hostpath-sc 创建一个名称为 pv-volume 的 pvc，容量为 10Mi
创建名称为 web-server 的pod，将 nginx 容器的 /usr/share/nginx/html 目录使用该 pvc 挂载
将上述 pvc 的大小从 10Mi 更新为 70Mi，并记录本次变更

解析：

根据官方文档拷贝一个PVC，修改参数，不确定的地方就是用 kubectl 的 explain 即可
通过 dry-run + -o yaml 形式生成一个 nginx 的pod，然后添加 volumeMounts 和 volume(不熟悉的话直接从 daemonset 案例中拷贝下来加以修改就行)
通过 edit 修改 pvc，别忘了 --record 参数
---
切换 context 

vim 13.1.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-volume
spec:
  accessModes:
    - ReadWriteOnce   (这个保留么？是的)
  resources:
    requests:
      storage: 10Mi
  storageClassName: csi-hostpath-sc

vim 13.2.yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-server
spec:
  containers:
    - name: nginx  (name和image是同一个名字)
      image: nginx
      volumeMounts:
      - mountPath: "/usr/share/nginx/html"
        name: mypv
  volumes:
    - name: mypv
      persistentVolumeClaim:
        claimName: pv-volume
# 13.3
kubectl edit pvc pv-volume --record 
更改 10Mi 为 70Mi

以上是笔者做题的方法，但是最好快要结束的时候检查发现是 pending 状态，所以导致后续 pod 也是 pending 状态，从而这个大题全部挂了；
笔者事后回顾，发现思路基本没啥问题，但笔者当时忘记了确认 csi-hostpath-sc 是否正常，所以仅有的可能就是没有切换到正确的 context 中从而导致pending的；这题的大失误也算是笔者比较粗细导致的吧。
---
任务：

创建一个名字为pv-volume，类型为csi-hostpath-sc，大小为10M的pvc。
创建一个pod,使用第一步创建的pvc,挂在地址是/usr/share/nginx/html。
将第一步创建的pv-volume大小更改为70M。
答案：

Vim pv-volume.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-vloume
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Mi
  storageClassName: csi-hostpath-sc

Kubectl apply -f pv-volume.yaml
Vim web-server.yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-server
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
      - mountPath: /usr/share/nginx.html
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: pv-volume

Kubectl descript pod web-server
Kubectl edit pvc pv-volume --record（将10Mi改成70Mi，保存即可）

---







===
L*** CKA2021最新真题解析14-监控 pod 的日志 (5)
===
###################################
命令是kubectl logs <pod name>
###################################
题目概述
监控 foobar pod 中的日志
获取包含 unable-to-access-website 的日志，并将日志写入到 /opt/KUTR00101/foobar
---
切换 context
kubectl logs foobar | grep unable-to-access-website > /opt/KUTR00101/foobar
---

===
L*** CKA2021最新真题解析15-添加 sidecar 容器并输出日志
===
###################################
这道题要使用输出的yaml文件。
要选择的yaml是Here's a configuration file for a pod that has two sidecar containers下面的
emptyDir 是容器共享的 volume.

###################################

(https://kubernetes.io/docs/concepts/cluster-administration/logging/) 其中选择：Here's a configuration file for a pod that has two sidecar containers下的yaml.
题目概述
添加一个 sidecar 容器 (使用busybox 镜像)到已有的 pod 11-factor-app 中
确保 sidecar 容器能够输出 /var/log/11-factor-app.log 的信息
使用 volume 挂载 /var/log 目录，确保 sidecar 能访问 11-factor-app.log 文件
---
解析：

通过 kubectl get pod -o yaml 的方法备份原始 pod 信息，删除旧的pod 11-factor-app
copy 一份新 yaml 文件，添加 一个名称为 sidecar 的容器
新建 emptyDir 的卷，确保两个容器都挂载了 /var/log 目录
新建含有 sidecar 的 pod，并通过 kubectl logs 验证
---
切换 context

apiVersion: v1
kind: Pod
metadata:
  name: 11-factor-app
spec:
  containers:
  - name: 11-factor-app
    image: busybox
    args:
    - /bin/sh
    - -c
    - >
      i=0;
      while true;
      do
        echo "$i: $(date)" >> /var/log/11-factor-app.log;
        i=$((i+1));
        sleep 1;
      done      
    volumeMounts:
    - name: varlog
      mountPath: /var/log
  - name: sidecar
    image: busybox
    args: [/bin/sh, -c, 'tail -n+1 -f /var/log/11-factor-app.log']
    volumeMounts:
    - name: varlog
      mountPath: /var/log
  volumes:
  - name: varlog
    emptyDir: {}

该题目 pod 内容笔者没法记全，不能直接复现，但其原理和考点链接中的官方案例极为相似，看懂了这个案例自然就能完成考题了；
实际考题中有一个运行中的pod,该 pod 也没有那么多复杂的参数，但它可以把日志写入到指定的目录；

解题思路：
先用kubectl get pod name -o yaml > podname.yaml 获取到yaml文件，然后删除旧的 pod；
再重新 copy 一个新的 yaml 添加 sidecar 容器，并在两个容器中都挂载 emtpyDir 到 /var/log目录，最后通过apply 生成带 sidecar 的 pod；
pod 正常拉起后,通过 kubectl logs 11-factor-app sidecar 确认能正常输入日志即可.

---
(bbs)
任务：

使用kubectl config use-context k8s。

已经有一个pod，用于存储记录kubectl的logs。

已有的名字为11-factor-app的pod,添加一个容器sidicar,使用busybox镜像，命令为/binsh -c “tail -n+1 f /var/log/11-factor-app.log”。
将新容器(看数据)和老容器(写数据)，都挂在存储到/var/log。
答案：

kubectl get pod 11-factor-app -o yaml > app.yaml(输出其yaml文件)
vim app.yaml(先备份一份，删除  managed 部分，删除status 部分，不删会有问题。)
在container下添加：

containers:
  - name: sidecar
    image: busybox
    args: [/bin/sh, -c,'"tail -n+1 -f /var/log/11-factor-app.log"']
    volumeMounts:
    - name: varlog
      mountPath: /var/log
在原容器下添加：
    volumeMounts:
    - name: varlog
      mountPath: /var/log
注意：pod的volumes为emptyDir。
volumes:
  - name: varlog
    emptyDir: {}

kubectl delete pod 11-factor-app
kubectl apply -f app.yaml
---





===
L*** CKA2021最新真题解析16-查看 cpu 使用率最高的 pod (5)
===
###################################
要用 -A 来找所有的pods， 要用-l 来筛选标签
###################################
题目概述
查找 label 为 name=cpu-loader 的 pod
筛选出 cpu 负载最高的那个 pod，并将名称 追加 到 /opt/KUTR00401/KUTR00401.txt

使用top命令，结合 -l label_key=label_value 和 --sort-by=cpu 过滤出目标即可
---

> kubectl top pod -A -l xxx=xxx --sort-by='cpu'

---
切换到kubectl config use-context k8s。

Kubectl top pod -l name=cpu-utilizer (查看哪个pod的CPU使用率最高，如pod名为websvr)
echo websvr >/opt/KUTR00401/KUTR00401.txt

---


===
L*** CKA2021最新真题解析17-排查集群中故障节点 (13)
===
###################################
OK
###################################
节点 wk8s-node-0 状态为 NotReady，查看原因并恢复其状态为 Ready
确保操作为持久的。
---
(视频)
>kubectl get nodes
>ssh 这个节点
>sudo -i
>systemctl status kubelet
>systemctl start kubelet
>systemctl enable kubelet

---
(文字)
切换 context 

kubectl get nodes
ssh wk8s-node-0
sudo -i 
systemctl status kubelet
systemctl enable kubelet
systemctl restart kubelet
systemctl status kubelet

再次 get nodes， 确保节点恢复 Ready 状态
---



使命令完整的命令：
source <(kubectl completion bash)



---
