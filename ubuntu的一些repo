	* 1. 手动更改
	用你熟悉的编辑器打开：/etc/apt/sources.list
	替换默认的http://archive.ubuntu.com/为http://mirrors.aliyun.com/
	* 2. ubuntu 20.04 LTS (focal) 配置如下

```
	deb https://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
	deb-src https://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
	
	deb https://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
	deb-src https://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
	
	deb https://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
	deb-src https://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
	
	# deb https://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
	# deb-src https://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
	
	deb https://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
	deb-src https://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
```
	* 3. ubuntu 22.04 LTS (jammy) 配置如下

```
	deb https://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
	deb https://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
	deb https://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
	# deb https://mirrors.aliyun.com/ubuntu/ jammy-proposed main restricted universe multiverse
# deb-src https://mirrors.aliyun.com/ubuntu/ jammy-proposed main restricted universe multiverse
	deb https://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
```
	* 4. ubuntu 23.04 (lunar) 配置如下

```
	deb https://mirrors.aliyun.com/ubuntu/ lunar main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ lunar main restricted universe multiverse
	
	deb https://mirrors.aliyun.com/ubuntu/ lunar-security main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ lunar-security main restricted universe multiverse
	
	deb https://mirrors.aliyun.com/ubuntu/ lunar-updates main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ lunar-updates main restricted universe multiverse
	
	# deb https://mirrors.aliyun.com/ubuntu/ lunar-proposed main restricted universe multiverse
# deb-src https://mirrors.aliyun.com/ubuntu/ lunar-proposed main restricted universe multiverse
	
	deb https://mirrors.aliyun.com/ubuntu/ lunar-backports main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ lunar-backports main restricted universe multiverse
	ubuntu 24.04 (noble) 配置如下
	deb https://mirrors.aliyun.com/ubuntu/ noble main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ noble main restricted universe multiverse
	
	deb https://mirrors.aliyun.com/ubuntu/ noble-security main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ noble-security main restricted universe multiverse
	
	deb https://mirrors.aliyun.com/ubuntu/ noble-updates main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ noble-updates main restricted universe multiverse
	
	# deb https://mirrors.aliyun.com/ubuntu/ noble-proposed main restricted universe multiverse
# deb-src https://mirrors.aliyun.com/ubuntu/ noble-proposed main restricted universe multiverse
	
	deb https://mirrors.aliyun.com/ubuntu/ noble-backports main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ noble-backports main restricted universe multiver
```
	* 6. Ubuntu docker 源和安装

```
	# step 1: 安装必要的一些系统工具
sudo apt-get update
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
# step 2: 安装GPG证书
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# Step 3: 写入软件源信息
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
# Step 4: 更新并安装Docker-CE
sudo apt-get -y update
sudo apt-get -y install docker-ce
	# 安装指定版本的Docker-CE:
# Step 1: 查找Docker-CE的版本:
# apt-cache madison docker-ce
# docker-ce | 17.03.1~ce-0~ubuntu-xenial | https://mirrors.aliyun.com/docker-ce/linux/ubuntu xenial/stable amd64 Packages
# docker-ce | 17.03.0~ce-0~ubuntu-xenial | https://mirrors.aliyun.com/docker-ce/linux/ubuntu xenial/stable amd64 Packages
# Step 2: 安装指定版本的Docker-CE: (VERSION例如上面的17.03.1~ce-0~ubuntu-xenial)
# sudo apt-get -y install docker-ce=[VERSION]
```
	* 7. kubectl 源安装

```
	apt-get update && apt-get install -y apt-transport-https
mkdir /etc/apt/keyrings/&&cd /etc/apt/keyrings/
curl -fsSL https://mirrors.aliyun.com/kubernetes-new/core/stable/v1.28/deb/Release.key |
    gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://mirrors.aliyun.com/kubernetes-new/core/stable/v1.28/deb/ /" |
    tee /etc/apt/sources.list.d/kubernetes.list
apt-get update
apt-get install -y kubelet kubeadm kubectl
8.SUSE源
	sudo zypper ar -fc https://mirrors.aliyun.com/opensuse/distribution/leap/15.2/repo/oss openSUSE15.2-Aliyun-OSS
sudo zypper ar -fc https://mirrors.aliyun.com/opensuse/distribution/leap/15.2/repo/non-oss openSUSE15.2-Aliyun-NON-OSS
sudo zypper ar -fc https://mirrors.aliyun.com/opensuse/update/leap/15.2/oss openSUSE15.2-Aliyun-UPDATE-OSS
sudo zypper ar -fc https://mirrors.aliyun.com/opensuse/update/leap/15.2/non-oss openSUSE15.2-Aliyun-UPDATE-NON-OSS
```
