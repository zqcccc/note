# K8S

学 CICD 的时候，接触到了 Kubernetes，简称 K8s，之前总是听说后端的人会说到这个词，也一直不知道是干嘛的，而我刚好有 3 台 Oracle 白嫖的服务器，那正好可以用来练练手。

学 k8s 的前置是你接触过 docker，对 Linux 有大概的了解，当然是懂的越多越好，不过我也懂的不是很多就开始了，发现大学的那些基础是真的重要，出来混早晚要还的。

Docker 让容器技术彻底火了，因为传统的部署方式，都是运维人员把资源部署到虚拟机上，需要搭建很多环境相关的配置，这样在后续的迭代的时候，不同的人经手同一台机器，压根就不知道各自对这个机器做了什么，迭代是非常不稳定的，所以就有了**不可变基础设施**这样的概念出现了，容器就是这样的作用，而且 docker 的容器是非常轻量的，做到了 app 级别的隔离，启动非常快，学习 docker 上手真的非常简单，以前配置非常麻烦的各种环境（后端开发，数据库）现在都可以超快的运行起来。

其实容器化技术也不是只有 docker 一家，那为了更通用的镜像能在各家平台上流转，那就需要有行业标准，docker 也是不断改变了架构，我在写下这个笔记的时候 docker 其实已经更新的到了 20.10.x 的版本了，平时敲得 docker 命令更像是一个客户端，真正做事的其实是 containerd 这个容器运行时，如果你之前没有接触过 docker 和 K8s，这个视频非常推荐你去学习一下 https://www.bilibili.com/video/BV1AS4y147JW

## 安装

容器火了之后，就需要 K8s 来做大规模部署容器的， 首先我们先安装好 K8s，首先先说，因为机器并不在国内，所以也就没有任何替换源的操作，如果需要替换源的话可以参考[这个视频](https://www.bilibili.com/video/BV1Qt4y1H7fV)的安装教程，这个视频讲的是 1.24 版本的手动安装

我是三台机器，两台是 x86 的，一台是 arm 的，是不是有点奇葩，我装的都是 Ubuntu 22。如果没有服务器的话，自己创建几个虚拟机也可以的。

安装的话先看这个[官方文档](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)，前面都是一些介绍

所有的服务器节点都要有下面的这些步骤，有的命令行工具可以然后多个窗口一起执行命令会方便很多

先关闭 swap (如果你的机器开启的话)

```bash
free -m # 查看 swap 是不是 0
vim /etc/fstab # 把这个文件里的 swap 相关的配置删掉或者注释
swapoff -a # 关闭 swap, -a 是关闭所有的 swap 区
```

然后看到官方文档开始装容器运行时，我们这里就装 containerd

[Container Runtimes containerd| Kubernetes](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd)

到这个 containerd github 安装介绍 [getting started with containerd](https://github.com/containerd/containerd/blob/main/docs/getting-started.md)，最上面写的都是手动安装的步骤，就是一步一步按它说的做就行，我觉得挺麻烦就直接 apt 安装了，如果你的系统不是 ubuntu 你也可以用你的系统对应的包管理工具安装

[Install Docker Engine on Ubuntu | Docker Documentation](https://docs.docker.com/engine/install/ubuntu/) 根据 containerd 安装文档如果 Ubuntu 用包管理工具安装就来到了安装 docker 的网站，按照文档的步骤添加源，只是在安装的时候只装 containerd 这个就行了

```bash
sudo apt-get install containerd.io
```

docker 选装就行

```bash
systemctl status containerd # 看下 containerd 能用了没
containerd config default > /etc/containerd/config.toml # 生成一个默认的 containerd 配置
```

刚刚生成的这个 containerd 配置文件需要改一个地方

```bash
vim /etc/containerd/config.toml
# 然后按/Systemd回车wwdwa空格true
# k8s 官网的文档写的，目的为了把 SystemdCgroup 改成 SystemdCgroup = true
# 国内环境还要替换的 pause 镜像的地址，可以自行搜索
# 保存退出
sudo systemctl restart containerd # 重启 containerd 
```

在运行时官网的上面还有一些命令要执行

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system
```

然后就开始安装 kubelet kubeadm kubectl

```bash
# Update the apt package index and install packages needed to use the Kubernetes apt repository:
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl

# Download the Google Cloud public signing key:
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

# Add the Kubernetes apt repository:
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

# Update apt package index, install kubelet, kubeadm and kubectl, and pin their version:
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

## 初始化

后面我们要用 flannel 网络插件，所以 pod 网段要指定 `10.244.0.0/16`，国内环境还要指定镜像地址（用 `--image-repository`），然后我们在主节点初始化

```bash
kubeadm init --pod-network-cidr 10.244.0.0/16
```

初始化成功后，会出现一些命令，你跟着复制执行就行了，不过最后有一条是 join 的命令是给其他工作节点执行的

然后来到 flannel GitHub 地址 [GitHub - flannel-io/flannel: flannel is a network fabric for containers, designed for Kubernetes](https://github.com/flannel-io/flannel)

```bash
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
# flannel 起来之后查看一下 pods
kubectl get po -A
```

如果这里的 dns 有问题，可以执行下面的命令，我之前初始化太多次集群了，网络被我搞的乱七八糟，很容易遇到这个问题

```bash
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
iptables -F
```

然后删除 dns pod，因为 dns 的 pod 是使用 deployment 部署的，所以会自动重启，删除也没有关系

```bash
kubectl delete -n kube-system po coredns-xxxx
```

## 实践

官方的文档太多内容了，想工具书一样让人望而却步，我反正是看不下去，如果你跟我一样的话，这里推荐一本书《Kubernetes in Action》虽然有些年份了，但是里面的内容还是非常实用的，有坑但也不是很难

而且有 [GitHub - luksa/kubernetes-in-action: Code from the Kubernetes in Action book](https://github.com/luksa/kubernetes-in-action) 仓库，yaml 配置直接复用，很多人都帮你把坑都踩过一遍了，有问题可以先在 issue 里找找

## 小技巧

[Linux 系统中的 Bash 自动补全功能 | Kubernetes](https://kubernetes.io/zh/docs/tasks/tools/included/optional-kubectl-configs-bash-linux/)，后面这个`kubectl`命令会敲特别多变，强烈建议加上自动补全

`crictl` 是用来管理 containerd 的，命令的用法跟 docker 基本一样，k8s 默认用的 containerd 创建的容器可以用 `crictl` 来查看
