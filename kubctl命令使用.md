```
root@k8s-master:~# kubectl top pod -A --sort-by=CPU
error: --sort-by accepts only cpu or memory
root@k8s-master:~# kubectl top pod -A --sort-by=cpu
NAMESPACE          NAME                                       CPU(cores)   MEMORY(bytes)   
kube-system        kube-apiserver-k8s-master                  41m          321Mi           
kube-system        calico-node-qfsp6                          39m          91Mi            
kube-system        calico-node-btm4r                          32m          142Mi           
kube-system        calico-node-57hcx                          29m          89Mi            
kube-system        kube-controller-manager-k8s-master         14m          48Mi            
default            nginx                                      13m          31Mi            
kube-system        etcd-k8s-master                            13m          51Mi            
kube-system        calico-kube-controllers-566dc76669-bvhgz   5m           17Mi            
kube-system        kube-scheduler-k8s-master                  4m           17Mi            
kube-system        coredns-6d8c4cb4d-5tm7w                    2m           12Mi            
kube-system        metrics-server-7b5798f8d9-bw7nq            2m           16Mi            
website-frontend   jenkins                                    2m           524Mi           
kube-system        kube-proxy-4tkv6                           1m           11Mi            
kube-system        coredns-6d8c4cb4d-lrnkh                    1m           12Mi            
kube-system        kube-proxy-gwbfk                           1m           11Mi            
kube-system        kube-proxy-8vjc6                           1m           10Mi            
default            nginx-deployment-54644c59bd-zbkx5          0m           2Mi             
default            nginx-deployment-54644c59bd-7bmwp          0m           2Mi             
default            nginx-app-659c4c484d-vkk5f                 0m           2Mi             
kube-system        fluentd-elasticsearch-8758v                0m           7Mi             
kube-system        fluentd-elasticsearch-5zp75                0m           5Mi             
default            nginx-deployment-54644c59bd-875nh          0m           2Mi             
default            nginx-app-659c4c484d-vbdk2                 0m           2Mi             
default            nginx-app-659c4c484d-nvm7j                 0m           2Mi             
default            fron-end                                   0m           3Mi             
root@k8s-master:~# kubectl top pod -A --sort-by=mem
error: --sort-by accepts only cpu or memory
root@k8s-master:~# kubectl top pod -A --sort-by=memory
NAMESPACE          NAME                                       CPU(cores)   MEMORY(bytes)   
website-frontend   jenkins                                    2m           524Mi           
kube-system        kube-apiserver-k8s-master                  41m          321Mi           
kube-system        calico-node-btm4r                          32m          142Mi           
kube-system        calico-node-qfsp6                          39m          91Mi            
kube-system        calico-node-57hcx                          29m          89Mi            
kube-system        etcd-k8s-master                            13m          51Mi            
kube-system        kube-controller-manager-k8s-master         14m          48Mi            
default            nginx                                      13m          31Mi            
kube-system        kube-scheduler-k8s-master                  4m           17Mi            
kube-system        calico-kube-controllers-566dc76669-bvhgz   5m           17Mi            
kube-system        metrics-server-7b5798f8d9-bw7nq            2m           16Mi            
kube-system        coredns-6d8c4cb4d-5tm7w                    2m           12Mi            
kube-system        coredns-6d8c4cb4d-lrnkh                    1m           12Mi            
kube-system        kube-proxy-gwbfk                           1m           11Mi            
kube-system        kube-proxy-4tkv6                           1m           11Mi            
kube-system        kube-proxy-8vjc6                           1m           10Mi            
kube-system        fluentd-elasticsearch-8758v                0m           7Mi             
kube-system        fluentd-elasticsearch-5zp75                0m           5Mi             
default            fron-end                                   0m           3Mi             
default            nginx-deployment-54644c59bd-7bmwp          0m           2Mi             
default            nginx-app-659c4c484d-vbdk2                 0m           2Mi             
default            nginx-app-659c4c484d-vkk5f                 0m           2Mi             
default            nginx-app-659c4c484d-nvm7j                 0m           2Mi             
default            nginx-deployment-54644c59bd-875nh          0m           2Mi             
default            nginx-deployment-54644c59bd-zbkx5          0m           2Mi             
root@k8s-master:~# kubectl top pod -A --sort-by=cpu
NAMESPACE          NAME                                       CPU(cores)   MEMORY(bytes)   
kube-system        calico-node-btm4r                          43m          142Mi           
kube-system        kube-apiserver-k8s-master                  41m          321Mi           
kube-system        calico-node-57hcx                          39m          90Mi            
kube-system        calico-node-qfsp6                          25m          93Mi            
kube-system        etcd-k8s-master                            13m          51Mi            
kube-system        kube-controller-manager-k8s-master         13m          48Mi            
default            nginx                                      12m          31Mi            
kube-system        metrics-server-7b5798f8d9-bw7nq            4m           16Mi            
kube-system        kube-scheduler-k8s-master                  3m           17Mi            
kube-system        coredns-6d8c4cb4d-5tm7w                    2m           12Mi            
kube-system        coredns-6d8c4cb4d-lrnkh                    2m           12Mi            
kube-system        calico-kube-controllers-566dc76669-bvhgz   2m           17Mi            
website-frontend   jenkins                                    2m           524Mi           
kube-system        kube-proxy-4tkv6                           1m           11Mi            
kube-system        kube-proxy-8vjc6                           1m           10Mi            
kube-system        kube-proxy-gwbfk                           1m           11Mi            
kube-system        fluentd-elasticsearch-8758v                0m           7Mi             
default            nginx-deployment-54644c59bd-7bmwp          0m           2Mi             
default            nginx-app-659c4c484d-vkk5f                 0m           2Mi             
default            nginx-deployment-54644c59bd-zbkx5          0m           2Mi             
default            nginx-deployment-54644c59bd-875nh          0m           2Mi             
kube-system        fluentd-elasticsearch-5zp75                0m           5Mi             
default            nginx-app-659c4c484d-vbdk2                 0m           2Mi             
default            nginx-app-659c4c484d-nvm7j                 0m           2Mi             
default            fron-end                                   0m           3Mi             
root@k8s-master:~# 
```
