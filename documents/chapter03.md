## （3） 使用标签组织pod
### 1 介绍标签
标签是一种简单去功能强大的K8s特性，不仅可以组织pod，也可以组织所有其他的K8s资源。
### 2 创建pod时制定标签
```
apiVersion: v1
kind: Pod
metadata:
  name: kubia-manual-v2
  labels:
    create_method: manual
    env: prod
spec:
  containers:
  - image: luksa/kubia
    name: kubia
    ports:
    - containerPort: 8080
      protocol: TCP
```
显示pod标签

    kubectl get po --show-labels
### 3.修改现有pod的标签
```
# 添加标签
kubectl label po {podName} {labelTag}={labelName}
# 修改现有标签
kubectl label po {podName} {labelTag}={labelName} --overwrite
```
## 通过标签选择器列出pod子集
### 使用标签选择器列出pod
```
# 列出指定标签值的pod
kubectl get po -l {labelTag}={labelName}
# 列出具有指定标签的pod
kubectl get po -l {labelTag}
# 列出没有制定标签的pod
kubectl get po -l '!{labelTag}'
```
还有更多的选择方式参考官方文档

## (5) 使用标签和选择器来约束pod调度
### 1 使用标签分类工作节点
```
kubectl label node {nodeName} {labelTag}={labelName}
```
### 2 将pod调度到特定节点
```
apiVersion: v1
kind: Pod
metadata:
  name: kubia-gpu
spec:
  nodeSelector:
    gpu: "true"
  containers:
  - image: luksa/kubia
    name: kubia
    ports:
    - containerPort: 8080
      protocol: TCP
```
### 3 调度到一个特定节点
我们可以将pod调度到某个特定的节点，由于每个节点都有一个唯一的标签，其中键为kubernetes.io/hostname，值为该节点的实际主机名，不过不建议这么做。

## （6） 注释pod
注释和标签一样也是键值对，可是注释不是保存标识信息而存在的。
注释在metadata.annotations下
添加注释

    kubectl annotate pod {podName} {annName}={annInfo}

## (7) 使用命名空间对资源进行分组
资源名称只需在命名空间内保持唯一即可,因此两个不同的命名空间可以包含同名的资源。命名空间为资源名称提供了一个作用于，在多用户分组操作等都有很好的组织效果。
### 1 发现命名空间及其pod
```
# 列出集群所有的命名空间
kubectl get ns
# 列出制定集群的pod
kubectl get po --namespace(-n) {namespace}
```
### 2 创建一个命名空间
```
apiVersion: v1
kind: Namespace
metadata:
  name: custom-namespace
```
也可以使用命令行：

    kubectl create namespace {namespace}
### 3 管理其他命名空间中的对象
```
# 指定namespace
kubectl create -f {YAML.FILE} -n {namespace}
# 切换namespace
kubectl config set-context \
    $(kubectl config current-context) \
    --namespace {namespace}
# 系统中可以设置别名
alias kcd='kubectl config set-context \
    $(kubectl config current-context \
    --namespace'
kcd {namespace}
```
### 4 命名空间提供的隔离
尽管只允许你对属于特定命名空间的对象进行操作，可实际上命名空间之间并不提供正在运行的对象的任何隔离。

## （8） 停止和移除pod
删除pod的过程中，实际上我们指示K8s终止pod中的所有容器。K8s向进程发送一个SIGTERM信号并等待一定秒数（默认30秒），使其正常关闭。如果没有关闭，则通过SIGKILL终止，因此，为了确保你的进程总是正常关闭的，进程需要正确处理SIGTERN信号。
```
# 按名称删除pod
kubectl delete po {podName}
# 使用标签选择器删除pod
kubeclt delete po -l {labelSeletor}
# 通过删除整个命名空间来删除pod
kubectl delete ns {namespace}
# 删除命名空间所有的pod，保留命名空间
kubectl delete po --all
# 删除命名空间所有的资源
kubectl delete all --all
```