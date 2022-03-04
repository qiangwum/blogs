  ## 依次执行如下步骤：
  ### 关闭swap
  
  ### 永久禁用，打开/etc/fstab注释掉swap那一行。
  ```sh
  swapoff -a
  sed -i 's/.*swap.*/#&/' /etc/fstab
  ```
  ### 关闭防火墙
  ```sh
  systemctl disable ufw
  systemctl stop ufw
  ```
  ### 修改内核参数
`  modprobe br_netfilter`
```sh
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
sudo curl -s https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add -

sudo tee /etc/apt/sources.list.d/kubernetes.list <<-'EOF'
deb https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial main
EOF
```
  ### 刷新软件仓库
  `sudo apt-get update`
  ### 查看可用的k8s版面
  `apt-cache madison kubeadm       //可以不执行`

  `sudo dpkg --configure -a        //可以不执行`
  
  `apt --fix-broken install -y     //可以不执行`
  ### 安装docker
  `apt-get install -y docker.io`

 
    ### 优化docker参数
```sh
cat <<EOF >/etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "registry-mirrors": ["https://pzpl72fb.mirror.aliyuncs.com"],
  "storage-driver": "overlay2",
  "storage-opts": ["overlay2.override_kernel_check=true"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m",
    "max-file": "3"
  }
}
EOF
```
  
  
### enable and start 
`systemctl enable docker;systemctl start docker`
  ### 安装k8s包，这里安装的是1.22.6版本，可以根据"查看可用版本章节选择其他版本"
  apt-get install -y kubelet=1.22.6-00 kubeadm=1.22.6-00 kubectl=1.22.6-00   
  
- - -
  
__以上步骤所有master节点和work节点都要执行__
  
  ### 启动kubelet服务
  systemctl enable kubelet && systemctl start kubelet
  ### 查看需要哪些image(可以不做)
  kubeadm config images list --kubernetes-version=v1.22.6   

  kubeadm init  --image-repository registry.aliyuncs.com/google_containers --kubernetes-version=v1.22.6   --pod-network-cidr=10.244.0.0/16
　
  __安装完成后查看查看node状态是not ready,并且coredns相关pod状态也异常，需要安装网络插件才能正常__
  ### 安装网络插件calico
  kubectl apply -f https://gitee.com/qiangwum/scripts/raw/master/calico.yaml
  
  
  
  至此，安装已经基本完成，但是kubectl top 相关命令不能使用，如需要使用，
  需要安装metrics-server
  
  kubectl apply -f https://raw.githubusercontent.com/qiangwum/script/main/metrics-server.yaml
  
  
  #### 官网的yaml文件需要做几处修改，比如image源，证书校验等，上面是我修改过的，经过测试可用
  kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
  ![yaml差异](https://github.com/qiangwum/PicGo_img/blob/master/Snipaste_2022-02-16_15-43-20.png?raw=true)
  
  
  
  refer:
  > * https://github.com/kubernetes-sigs/metrics-server/
  > * http://blog.ljmict.com/?p=98
  
