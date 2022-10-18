# K8s 实践

本文主要讲的是《Kubernetes in Action》的一些坑，因为书是 2017 年写的，所以有些东西就不太一样了

学习 k8s 的时候，再暴露网络的那个地方，会有个外部负载均衡器（Load Bablence 简称 LB）的东西，主要是通过这个均衡器来分配请求到各个服务器上，整个均衡器是要花钱的，对于我们学习来说，其实没有什么必要，直接在主节点上部署以 hostnetwork 的方式部署一个 nginx ingress controller 是差不多的，只不过这样的话流量都要走主节点进来，主节点的带宽大小会比较重要。

[Installation Guide - NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/deploy/#bare-metal-clusters)

```bash
wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.3.0/deploy/static/provider/baremetal/deploy.yaml
```

在这个 yaml 文件中找到唯一的 Deployment，在 `spec.template.spec` 下面设置 `hostNetwork: true`

我还设置了 `nodeSelector` 让它只部署到主节点

```bash
kubectl apply -f deploy.yaml
```

后面要暴露服务就可以用 ingress 去暴露你的服务，ingress 的配置相对会简单很多

存储章节的有用到 mongodb 的镜像，按照书中的进入 mongodb 的容器中执行 `mongo` 命令，会提示命令找不到，书中的 mongodb 是比较老的版本了，我在写这个文章的时候 mongdb 镜像的版本都到了 6.x 了，用的是 `mongosh` 去连接 mongodb 的
