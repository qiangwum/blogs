---
> CKA考试题目类型
---
1. Set configuration context $ kubectl config use-context k8s Monitor the logs of Pod foobar and Extract log lines corresponding to error file-not-found Write them to /opt/KULM00201/foobar
   ```
     kubectl logs foobar | grep file-not-found > /logs
   ```
---
2. List all PVs sorted by name saving the full kubectl output to /opt/KUCC0010/my_volumes . Use kubectl’s own functionally for sorting the output, and do not manipulate it any further.
   ```
     kubectl get pv --all-namespace --sort-by=.metadata.name > /opt/
   ```
---
3. Ensure a single instance of Pod nginx is running on each node of the kubernetes cluster where nginx also represents the image name which has to be used. Do no override any taints currently in place.
   Use Daemonsets to complete this task and use ds.kusc00201 as Daemonset name
   需要删除不需要的部分tolerations和volume
```
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
4. Add an init container to lumpy--koala (Which has been defined in spec file /opt/kucc00100/pod-spec-KUCC00100.yaml)The init container should create an empty file named /workdir/calm.txt
   If /workdir/calm.txt is not detected, the Pod should exit
   Once the spec file has been updated with the init container definition, the Pod should be created.
---
5.  Create a pod named kucc4 with a single container for each of the following images running inside (there may be between 1 and 4 images specified): nginx + redis + memcached + consul
```
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
6.  Schedule a Pod as follows:
    Name: nginx-kusc00101
    Nmage: nginx
    Node selector: disk=ssd
```
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
	  	disk: ssd
```
---
7.  Create a deployment as follows:
    Name: nginx-app
    Using container nginx with version 1.10.2-alpine
    the deployment should contain 3 replicas
    Next, deploy the app with new version 1.13.0-alpine by performing a rolling update and record that update.
    Finally, rollback that update to the previous version 1.10.2-alpine
```
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
8.  Create and configure the service front-end-service so it’s accessible through NodePort and routes to the existing pod named front-end
```
kubectl expose pod fron-end --name=front-end-service --port=80  --type=NodePort     //需要先创建pod我不知道考试需要自己创建不，kubectl run fron-end --image=nginx
```
---
9.   Create a Pod as follows:
	Name: jenkins
	Using image: jenkins
	In a new Kubenetes namespace named website-frontend 
```
kubectl create ns website-frontend
kubectl run Jenkins --image=jenkins --namespace=website-frontend     //pod name不能包含大写，不知道是不是题目的问题
```
---
10. Create a deployment spec file that will:
	Launch 7 replicas of the redis image with the label: app_env_stage=dev
	Deployment name: kual0020
Save a copy of this spec file to /opt/KUAL00201/deploy_spec.yaml (or .json)
When you are done, clean up (delete) any new k8s API objects that you produced during this task 
```
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
11. Create a file /opt/KUCC00302/kucc00302.txt that lists all pods that implement Service foo in Namespace production.
The format of the file should be one pod name per line
```
kubectl get svc -n production --show-labels | grep foo

kubectl get pods -l app=foo(label标签)  | grep -v NAME | awk '{print $1}' >> /opt/KUCC00302/kucc00302.txt
```



