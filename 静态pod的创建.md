1. login master node：查询--config所在路径，如下图可以看到--config=/var/lib/kubelet/config.yaml
```sh
root@k8s-master:~# systemctl status kubelet.service 
● kubelet.service - kubelet: The Kubernetes Node Agent
   Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
  Drop-In: /etc/systemd/system/kubelet.service.d
           └─10-kubeadm.conf
   Active: active (running) since Mon 2022-02-21 11:33:49 CST; 12min ago
     Docs: https://kubernetes.io/docs/home/
 Main PID: 94247 (kubelet)
    Tasks: 14 (limit: 4625)
   CGroup: /system.slice/kubelet.service
           └─94247 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --network-plugin=cni --pod-i
```
2. 在/var/lib/kubelet/config.yaml 查看是否有如下参数：
```yaml
staticPodPath: /etc/kubernetes/manifests      //如果没有就手动加上
```
3. 随意登录一台node：ssh k8s-node-01进入到/etc/kubernetes/manifests目录下
   创建一个pod的yaml 
   eg：
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: static-pod-test
spec:
  containers:
  - name: nginx
    image: nginx
```
4. 执行如下命令使新增参数生效
```sh
systemctl stop kubelet
systemctl daemon-reload
systemctl start kubelet
```
5. 验证：
```sh
root@k8s-master:/var/lib/kubelet# kubectl get pod 
NAME                         READY   STATUS      RESTARTS   AGE
pv-recycler                  0/1     Completed   0          64m
static-pod-test-k8s-node01   1/1     Running     0          48s
root@k8s-master:~# kubectl delete pod static-pod-test-k8s-node01
pod "static-pod-test-k8s-node01" deleted
root@k8s-master:~# kubectl get pod 
NAME                         READY   STATUS      RESTARTS   AGE
pv-recycler                  0/1     Completed   0          66m
static-pod-test-k8s-node01   1/1     Running     0          2s
```
__删也删不掉，难受，static-pod-test-k8s-node01，另外pod的命令也比较特殊。😁__
