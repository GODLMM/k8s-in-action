# 开始使用kubernetes
## 部署Node.js应用
### 创建应用
    kubectl run kubia --image=lukas/kubia --port=8080 --generator=run/v1

--image 指定要运行的容器镜像
--port 告诉K8s要监听的端口
--generator 通常不会使用它，他让K8s创建一个ReplicationController,而不是Deployment。
### 列出pods
    kubectl get pods(po)
### 列出ReplicationController
    kubectl get rc

## 访问应用
### 创建一个服务
    kubectl expose rc kubia --type=LoadBalancer --name kubia-http
### 列出服务
    kubectl get services(svc)


## 水平伸缩扩容
### 增加期望的样本数
    kubectl scale rc kubia --replicas=3
### 查看应用运行在那个节点上
    kubectl get pods -o wide
### 查看pod的其他细节
    kubectl describe pod {PodName}