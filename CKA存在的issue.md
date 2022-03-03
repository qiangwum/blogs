1. kubeadm 升级题目

 ```
>  hold – 标记指定软件包为保留(held back)，阻止软件自动更新         
>  unhold – 取消指定软件包的保留(held back)标记，解除阻止自动更新        //个人觉得可加可不加，不影响题目
 ```
 ssh master
 sudo -i
 source <(kubectl completion bash)
 kubectl drain master --ngnore-daemonsets
 apt-get install kubeadm=1.19.0=00
 kubadm version
 kubeadm upgrade apply 1.23.0 --etcd-upgrade=false   控制组件升级到1.23
 apt-get install kubectl=1.23.0-00 kubelet=1.23.0-00
 
  
 2. etcd数据备份问题
 需要加上 
 ### ETCDCTL_API=3    ！！！！！！！！！
 备份快照到/etc/data/etcd-snapshot.db目录；
 $ ETCDCTL_API=3etcdctl --endpoints="https://127.0.0.1:2379" --cacert=/opt/KUIN000601/ca.crt --cert=/opt/KUIN000601/etcd-client.crt --key=/opt/KUIN000601/etcd-client.key  snapshot save /etc/data/etcd-snapshot.db
 
 $ ETCDCTL_API=3  etcdctl --endpoints="https://127.0.0.1:2379" --cacert=/opt/KUIN000601/ca.crt --cert=/opt/KUIN000601/etcd-client.crt --key=/opt/KUIN000601/etcd-client.key   snapshot restore /var/lib/backup/etcd-snapshot-previoys.db --data-dir=/var/lib/etcd
 
 3. networkPolicy 问题
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: mywuqiang-network-policy
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
          project: my-app
    - podSelector:
        matchLabels: {}
    ports:
    - protocol: TCP
      port: 80
```
4. deployment 给 container 添加 port
```yaml
        ports:
        - containerPort: 80
          name: http
          protocol: TCP
 ```
 5. ingress
 [文档yaml](https://kubernetes.io/docs/concepts/services-networking/ingress/)
