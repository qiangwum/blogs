---
CKA考试题目类型  书签： [K8S](https://kubernetes.io/docs/home/)
---
#### 1. Set configuration context $ kubectl config use-context k8s Monitor the logs of Pod foobar and Extract log lines corresponding to error file-not-found Write them to /opt/KULM00201/foobar
   ```shell
     kubectl logs foobar | grep file-not-found > /logs
   ```
---
#### 2. List all PVs sorted by name saving the full kubectl output to /opt/KUCC0010/my_volumes . Use kubectl’s own functionally for sorting the output, and do not manipulate it any further.
   ```shell
     kubectl get pv --all-namespace --sort-by=.metadata.name > /opt/
   ```
---
#### 3. Ensure a single instance of Pod nginx is running on each node of the kubernetes cluster where nginx also represents the image name which has to be used. Do no override any taints currently in place.
#####   Use Daemonsets to complete this task and use ds.kusc00201 as Daemonset name
   需要删除不需要的部分tolerations和volume
```yaml
  apiVersion: apps/v1
  kind: DaemonSet
  metadata:
    name: fluentd-elasticsearch
    namespace: kube-system
    labels:
      k8s-app: fluentd-logging
  spec:
    selector:
      matchLabels:
        name: fluentd-elasticsearch
    template:
      metadata:
        labels:
          name: fluentd-elasticsearch
      spec:
  #      tolerations:
        # this toleration is to have the daemonset runnable on master nodes
        # remove it if your masters can't run pods
  #      - key: node-role.kubernetes.io/master
  #        operator: Exists
  #        effect: NoSchedule
        containers:
        - name: fluentd-elasticsearch
  #       image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
          image: nginx
  #        resources:
  #          limits:
  #            memory: 200Mi
  #          requests:
  #            cpu: 100m
  #            memory: 200Mi

```
---
#### 4. Add an init container to lumpy--koala (Which has been defined in spec file /opt/kucc00100/pod-spec-KUCC00100.yaml)The init container should create an empty file named /workdir/calm.txt
####   If /workdir/calm.txt is not detected, the Pod should exit
####   Once the spec file has been updated with the init container definition, the Pod should be created.

---
#### 5.  Create a pod named kucc4 with a single container for each of the following images running inside (there may be between 1 and 4 images specified): nginx + redis + memcached + consul
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kucc
spec:
  containers:
  - name: nginx
    image: nginx
  - name: redis
    image: redis
  - name: memcached
    image: memcached
  - name: consul
    image: consul
```
---
#### 6.  Schedule a Pod as follows:
*    Name: nginx-kusc00101
*    Nmage: nginx
*    Node selector: disk=ssd
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    disktype: ssd  
```
---
#### 7.  Create a deployment as follows:
   * Name: nginx-app
   * Using container nginx with version 1.10.2-alpine
   * the deployment should contain 3 replicas
   * Next, deploy the app with new version 1.13.0-alpine by performing a rolling update and record that update.
   * Finally, rollback that update to the previous version 1.10.2-alpine
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-app
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.11.9
```
```
kubectl set image deployment nginx-app nginx-app=nginx:1.12.0 --record  (nginx-app container名字)
kubectl rollout history deployment nginx-app
kubectl rollout undo deployment nginx-app    ///新版本好像没有这个命令，需要确认下考试版本
```
---
#### 8.  Create and configure the service front-end-service so it’s accessible through NodePort and routes to the existing pod named front-end
```sh
kubectl expose pod fron-end --name=front-end-service --port=80  --type=NodePort     //需要先创建pod我不知道考试需要自己创建不，kubectl run fron-end --image=nginx
```
---
#### 9.   Create a Pod as follows:
*	Name: jenkins
*	Using image: jenkins
*	In a new Kubenetes namespace named website-frontend 
```
kubectl create ns website-frontend
kubectl run Jenkins --image=jenkins --namespace=website-frontend     //pod name不能包含大写，不知道是不是题目的问题
```
---
#### 10. Create a deployment spec file that will:
- #### Launch 7 replicas of the redis image with the label: app_env_stage=dev

- #### Deployment name: kual0020

- #### Save a copy of this spec file to /opt/KUAL00201/deploy_spec.yaml (or .json)

- #### When you are done, clean up (delete) any new k8s API objects that you produced during this task 

```yaml
kubectl run kual00201 --image=redis --labels=app_enb_stage=dev --dry-run=client -oyaml > /opt/KUAL00201/deploy_spec.yaml 
root@k8s-master:~# cat /opt/KUAL00201/deploy_spec.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    app_enb_stage: dev
  name: kual00201
spec:
  containers:
  - image: redis
    name: kual00201
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
root@k8s-master:~# 
```
---
#### 11. Create a file /opt/KUCC00302/kucc00302.txt that lists all pods that implement Service foo in Namespace production.
#### The format of the file should be one pod name per line
```
kubectl get svc -n production --show-labels | grep foo

kubectl get pods -l app=foo(label标签)  | grep -v NAME | awk '{print $1}' >> /opt/KUCC00302/kucc00302.txt
```
---
#### 12. Create a Kubernetes Secret as follows:
- #### Name: super-secret

- #### Credential: alice  or username:bob 

- #### Create a Pod named pod-secrets-via-file using the redis image which mounts a secret named super-secret at /secrets

- #### Create a second Pod named pod-secrets-via-env using the redis image, which exports credential as TOPSECRET


* secrets
```yaml
root@k8s-master:~# echo 'bob' | base64
Ym9iCg==

apiVersion: v1
kind: Secret
metadata:
  name: super-secret
type: Opaque
data:
  username: Ym9iCg==
```
* pod
```
apiVersion: v1
kind: Pod
metadata:
  name: pod1
spec:
  containers:
  - name: mypod
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/secret"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: super-secret
```
* envpod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod2
spec:
  containers:
  - name: mycontainer
    image: nginx
    env:
      - name: ABC
        valueFrom:
          secretKeyRef:
            name: super-secret
            key: username
  restartPolicy: Never
```
* //验证一下，考试不用
```sh
root@k8s-master:~# kubectl exec -it pod1 -- bash
root@pod1:/data# ls
root@pod1:/data# cat /secret/username 
bob
root@pod1:/data# exit
exit
root@k8s-master:~# kubectl exec -it pod2 -- bash 
root@pod2:/# env | grep ABC
ABC=bob
root@pod2:/# 
```
---
13. Create a pod as follows:
	Name: non-persistent-redis
	Container image: redis
	Named-volume with name: cache-control
	Mount path: /data/redis
	It should launch in the pre-prod namespace and the volume MUST NOT be persistent.
```yaml
kubectl create ns pre-prod

apiVersion: v1
kind: Pod
metadata:
  name: non-presistent-redis
  namespace: pre-prod
spec:
  containers:
  - image: redis
    name: redis
    volumeMounts:
    - mountPath: /data/redis
      name: cache-control
  volumes:
  - name: cache-control
    emptyDir: {}
```
---
#### 14. deploy scale
```sh
kubectl scale deployment nginx-app --replicas=6

```
---
#### 15. Check to see how many nodes are ready (not including nodes tainted NoSchedule) and write the number to /opt/nodenum
```sh
kubectl get node | grep -w  Ready | wc -l     //ready 个数
kubectl describe nodes | grep Taints | grep -i noschedule | wc -l     //noschedule个数 
```
---
#### 16. From the Pod label name=cpu-utilizer, find pods running high CPU workloads and write the name of the Pod consuming most CPU to the file /opt/cpu.txt (which already exists)
```sh
kubectl top pod --sort-by=cpu --namespace kube-system  
```
---
#### 17.  Create a deployment as follows
- #### Name: nginx-dns

- #### Exposed via a service: nginx-dns

- #### Ensure that the service & pod are accessible via their respective DNS records

- #### The container(s) within any Pod(s) running as a part of this deployment should use the nginx image

- #### Next, use the utility nslookup to look up the DNS records of the service & pod and write the output to /opt/service.dns and /opt/pod.dns respectively.

- #### Ensure you use the busybox:1.28 image(or earlier) for any testing, an the latest release has an unpstream bug which impacts thd use of nslookup.

```sh
kubectl create deployment nginx-dns --image=nginx
kubectl expose deployment nginx-dns --port=80 --type=NodePort
root@k8s-master:~# kubectl run busybox -it --rm --image=busybox:1.28 -- sh 
If you don't see a command prompt, try pressing enter.
/ # nslookup nginx-dns
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      nginx-dns
Address 1: 10.107.102.138 nginx-dns.default.svc.cluster.local
/ # 
/ # 
```
---
#### 18. Create a snapshot of the etcd instance running at https://127.0.0.1:2379 saving the snapshot to the file path /data/backup/etcd-snapshot.db
- #### The etcd instance is running etcd version 3.1.10

- #### The following TLS certificates/key are supplied for connecting to the server with etcdctl

- #### CA certificate: /opt/KUCM00302/ca.crt

- #### Client certificate: /opt/KUCM00302/etcd-client.crt

- #### Clientkey:/opt/KUCM00302/etcd-client.key 
```sh
ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379  --cacert=ca.pem --cert=server.pem --key=server-key.pem  snapshot save /data/backup/etcd-snapshot.db
```
---
#### 19. Set the node labelled with name=ek8s-node-1 as unavailable and reschedule all the pods running on it.
```
kubectl get nodes -l name=ek8s-node-1
kubectl drain wk8s-node-1  
#当目标node上有daementSet时需要加以下参数，所以建议加上
#--ignore-daemonsets=true --delete-local-data=true --force=true
kubectl uncordon k8s-node0*    //恢复命令，考试不用
```
---
#### 20. A Kubernetes worker node, labelled with name=wk8s-node-0 is in state NotReady . Investigate why this is the case, and perform any appropriate steps to bring the node to a Ready state, ensuring that any changes are made permanent.
```
kubectl get nodes | grep NotReady
ssh node  
systemctl status kubelet
systemctl start kubelet   
systemctl enable kubelet
```
---
#### 21. Configure the kubelet systemd managed service, on the node labelled with name=wk8s-node-1, to launch a Pod containing a single container of image nginx named myservice automatically. Any spec files required should be placed in the /etc/kubernetes/manifests directory on the node.
> [参考blog](https://github.com/qiangwum/blogs/blob/main/%E9%9D%99%E6%80%81pod%E7%9A%84%E5%88%9B%E5%BB%BA.md)
---
#### 22.  Determine the node, the failing service and take actions to bring up the failed service and restore the health of the cluster. Ensure that any changes are made permanently.
#### The worker node in this cluster is labelled with name=bk8s-node-0
```
情形一：kubectl 命令能用 
kubectl get cs 健康检查  看manager-controller  是否ready   
如果不ready   systemctl start kube-manager-controller.service   
情形二：kubectl 命令不能用
2，ssh登陆到bk8 -master-0上检查服务，如master上的4大服务，
api-server/schedule/controllor-manager/etcd
systemctl list-utils-files | grep controller-manager    没有服务
systemctl list-utils-files | grep api-server       没有服务
3,此刻进入/etc/kubernetes/manifest  文件夹中，可以看到api-server.yaml  controller-manager.yaml等4个文件，说明这几个服务是以pod方式提供服务的。
4, systemctl status kubelet     看到是正常启动的，
说明api-server   controlloer-manager    etcd    schedule  这几个pod 没启动，
检查静态pod配置,在/var/lib/systemd/system/kubelet.service 这个文件里检查配置看到静态配置路径错误
考试环境把正确的/etc/kubernetes/manifest  换成了/etc/kubernetes/DODKSIYF 路径，此路径并不存在，把这个错误的路径换成到存放api/controller-manager/etcd/schedule这几个yaml文件存放的路径，重启Kubelet，排错完成。
再查看node啥的，就OK了
```
---
#### 23. Creae a persistent volume with name app-config of capacity 1Gi and access mode ReadWriteOnce. The type of volume is hostPath and its location is /srv/app-config

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-config
spec
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: slow
  hostPath:
    path: /srv/app-config
```
---
#### 24. 环境搭建
* 可以参考帖子[k8s安装](https://github.com/qiangwum/blogs/blob/main/ubuntu%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%AE%89%E8%A3%85k8s.md)
```sh
# master和node上:安装kubeam kubelet kubectl

cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system

sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

#master初始化
kubeadm init   --ignore-preflight-errors=xxx

#master安装网络
kubectl apply -f https://docs.projectcalico.org/v3.11/manifests/calico.yaml

#node加入集群
```
---
#### 24. TLS问题 （一道很长的题目，建议放弃，难度特别大）

> [yaml参考](https://github.com/qiangwum/script/blob/main/pod.yaml)
> 😁


