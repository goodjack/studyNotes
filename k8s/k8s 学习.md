### k8s 集群架构组件

master（主控节点）和node（工作节点）

master组件内包含：

- apiserver：集群统一入口，以restful方式，交给etcd存储
- scheduler：节点调度，选择node节点应用部署
- controller-manager：处理集群中常规后台任务，一个资源对应一个控制器
- etcd：存储系统，用于保存集群相关的数据

node组件：

- kubelet：管理本机容器
- kube-proxy：提供网络、负载均衡等操作

### Pod 的概念

- 最小部署单元

- 一组容器的集合
- 共享网络
- 生命周期短暂

#### 资源类型的配置

| 参数名                                      | 字段类型 | 说明                                                         |
| ------------------------------------------- | -------- | ------------------------------------------------------------ |
| version                                     | string   | k8s API 的版本，使用 kubectl api-versions 命令查询           |
| kind                                        | string   | 定义资源的类型和角色，如：Pod                                |
| metadata                                    | Object   | 元数据对象，固定值 metadata                                  |
| metadata.name                               | string   | 元数据对象的名字，如命名 Pod 的名字                          |
| metadata.namesapce                          | string   | 元数据对象的命名空间                                         |
| Spec                                        | Object   | 定义对象，固定值 Spec                                        |
| spec.containers[]                           | list     | 容器列表                                                     |
| spec.containers[].name                      | string   | 容器名字                                                     |
| sepc.containers[].image                     | string   | 定义要用到的镜像名称                                         |
| spec.containers[].imagePullPolicy           | string   | 默认值 Always：表示每次都尝试重新拉取镜像；Never：表示仅用本地镜像；IfNotPresent：如果本地有镜像则使用本地，没有就拉取镜像 |
| spec.containers[].command[]                 | list     | 指定容器启动命令，不指定则使用镜像打包时使用的启动命令       |
| spec.containers[].args[]                    | list     | 指定容器启动命令参数                                         |
| spec.containers[].workingDir                | string   | 指定容器工作目录                                             |
| spec.containers[].volumeMounts[]            | list     | 指定容器内部的存储卷配置                                     |
| spec.containers[].volumeMounts[].name       | string   | 可以被容器挂载的存储卷名称                                   |
| spec.containers[].volumenMounts[].mountPath | string   | 可以被容器挂载的存储卷路径                                   |
| spec.containers[].volumenMounts[].readonly  | string   | 设置存储卷路径的读写模式                                     |
| spec.containers[].ports[]                   | list     | 容器需要用到的端口列表                                       |
| spec.containers[].ports[].name              | string   | 指定端口名称                                                 |
| spec.containers[].ports[].containerPort     | string   | 指定容器需要监听的端口号                                     |
| spec.containers[].ports[].hostPort          | string   | 指定容器所在主机需要监听的端口号，设置了 hostPort 同一台主机无法启动该容器的相同副本，因为一台主机不能有相同的端口号 |
| spec.containers[].ports[].protocol          | string   | 指定端口协议，支持 tcp 和 udp，默认 tcp                      |
| spec.containers[].env[]                     | list     | 容器运行前需要设置的环境变量列表                             |
| spec.containers[].env[].name                | string   | 环境变量名称                                                 |
| spec.containers[].env[].value               | string   | 环境变量值                                                   |
| spec.containers[].resources                 | Object   | 指定资源限制和资源请求的值                                   |
| spec.containers[].resources.limits          | Object   | 设置容器运行时资源的运行上限                                 |
| spec.containers[].resources.limits.cpu      | string   | 指定 CPU 的限制，单位为 core 数                              |
| spec.containers[].resources.limits.memory   | string   | 指定 mem 内存限制                                            |
| spec.containers[].resources.requests        | Object   | 指定容器启动和调度时的限制设置                               |
| spec.containers[].resources.requests.cpu    | Object   | 容器启动时初始化可用数量                                     |
| spec.containers[].resources.requests.memory | string   | 内存请求，容器启动的初始化可用数量                           |
| spec.restartPolicy                          | string   | 定义 Pod 的重启策略，Always：默认值，pod 一旦终止运行，kubelet 服务都将重启它；onFailure：只有 pod 以非零退出码终止时，kubelet 才会重启该容器；Never：pod 终止后，kubelet 将退出码报告给 master，不会重启该 pod |
| spec.nodeSelector                           | object   | 定义 node 的 label 过滤标签，k:v 格式                        |
| spec.imagePullSecrets                       | object   | 定义 pull 镜像时使用 secret 名称，k:v 格式                   |
| spec.hostNetwork                            | bool     | 定义是否使用主机网络模式                                     |

#### Pod  的生命周期

- initcontainer 控制初始化的运行，每一个 init 容器都具有先后顺序，先定义的执行完毕后才会执行第二个

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    environment: dev
spec:
	initContainers:
	- name: first # 表示第一个init 容器，每 2s 查询一次，mydb 的服务名是否被解析，成功则执行下一步
	  image: busybox
	  command: ['sh','-c','until nslookup mydb;do echo waiting for mydb;sleep 2;done;']
  - name: two # 第二个 init 容器，每次间隔 2s 循环执行，知道解析成功 nginx 的服务名
    image: busybox
    command: ['sh','-c','until nslookup nginx; do echo waiting for nginx; slepp 2;done;']
  containers:
  - name: nginx
    image: nginx:1.19.0
  - name: mydb
    image: redis:6.0
```

**探针：探针是有由kubelet 对容器执行的定期诊断，有三种类型的处理程序：**

1. ExecAction：在容器内执行指定命令，如果命令退出时返回码为 0，则认为诊断成功
2. TCPSocketAction：对指定端口上的容器 IP 地址进行 TCP 检查，如果端口打开，则诊断认为成功
3. HTTPGetAction：对指定端口和路径上的容器的 IP 地址执行 HTTP Get 请求，如果响应 200<=code<400，则被认为诊断成功。

- readiness 检查 pod 的就绪状态

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: readiness-httpget-pod
  namspace: default
spec:
  containers:
  - name: readiness-httpget-container
    image: nginx:1.19.0
    imagePullPolicy: IfNotPresent
    readinessProbe:
      httpGet:
        port: 80
        path: /index.html
      initialDelaySeconds: 1 # 容器启动 1s 后进行检测
      periodSeconds: 3 # 每 3s 检测一次
```



- liveness 检查 pod 的存活状态，存活探针会一直存在

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-exec-pod
  namespace: default
spec:
  containers:
  - name: liveness-exec-container
    image: busybox
    imagePullPolicy: IfNotPresent
    command: ['/bin/sh','-c','touch /tmp/live; slepp 60;rm -rf /tmp/live; slepp 3600']
    # 使用的命令检测，检测文件是否存在
    livenessProbe:
      exec:
        command: ['test','-e','/tmp/live']
      initialDelaySeconds: 1
      periodSeconds: 3
```



- lifecycle 可以在 start 和 stop 时进行初始化和收尾操作

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: lifecycle-demo
spec:
  containers:
  - name: lifecycle-demo-container
    image: nginx:1.19.0
    lifecycle:
      postStart: # 在 pod 开始前，执行一条命令
        exec:
          command: ['/bin/bash','-c','echo star handler > /tmp/lifecycle']
      preStop: # 在 pod 关闭之前，执行一条命令
        exec:
          command: ['/bin/bash','-c','rm /tmp/lifecycle']
```





### controller

- 确保预期的pod副本数量
- 无状态应用部署
- 有状态应用部署
- 确保所有的node运行同一个pod
- 一次性任务和定时任务

#### 控制器类型

- ReplicationController 和 ReplicaSet
- Deployment
- DaemonSet
- StateFulSet
- Job/CronJob
- Horizontal Pod Autoscaling

#### ReplicationSet

> 用来确保容器应用的副本数始终保持在用户定义的副本数，如果有容器异常退出，会自动创建新的 Pod 来替代，如果异常多出来的容器也会自动回收。

```yaml
apiVersion: extensions/v1beta1
kind: ReplicaSet
metadata:
  name: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: redis
        env:
        - name: GET_HOST_FROM
          value: dns
        ports:
        - containerPort: 80
```



#### Deployment

> - 定义 Deployment 来创建 Pod 和 ReplicaSet
> - 滚动升级和回滚应用
> - 扩容和缩容
> - 暂停和继续 Deployment

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```



#### DaemonSet

> 确保全部或一些 Node 上运行一个 Pod 副本。当有 Node 加入集群时，也会为他们新增一个 Pod。当有Node 从集群移除时，这些 Pod 也会被回收。删除 DaemonSet 将会删除它创建的所有 Pod。

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: daemonset-apps
  labels:
    app: daemonset
spec:
  selector:
    matchLabels:
      name: daemonset-apps
  template:
    metadata:
      labels:
        name: daemonset-apps
    spec:
      containers:
      - name: daemonset-apps
        images: myapp:v1
```



#### Job

> Job 负责批处理任务，仅执行一次的任务，它保证批处理任务的一个或多个 Pod 成功结束。

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    metadata:
      name: pi
   spec:
     containers:
     - name: pi
       image: perl
       command: ["perl","-Mbignum=bpi","-wle","print bpi(2000)"]
     restartPolicy: Never
```



#### CronJob

> CronJob 管理基于时间的 Job
>
> - 在给定时间点只运行一次
> - 周期性的在给定时间点运行

#### StatefulSet

> StatefulSet 作为 Controller 为 Pod 提供唯一的标识。保证可以部署和 scale 顺序。

#### Horizontal Pod Autoscaling

> 根据服务器资源使用率自动缩放 Pod，提高集群的整体资源利用率。



### Service 概念

**Kubernetes Service 定义了一个抽象：一个 Pod 的逻辑分组，一种可以访问它们的策略 ---- 通常称为微服务。这一组 Pod 能够被 Service 访问到，通常是通过 Label Selector**

定义一组pod的访问规则

Service 能够提供负载均衡的能力，在使用时有限制：

- 只提供 4 层负载均衡的能力

### Service 的类型

1. cluster IP：默认类型，自动分配一个仅 cluster 内部可以访问的虚拟 IP
2. NodePort：在 clusterIP 基础上为 Service 在每台机器上绑定一个端口，这样就可以通过 NodeIP:NodePort 来访问该服务
3. LoadBalancer：在 NodePort 基础上，借助 cloud provider 创建一个外部负载军很气，并将请求转发到 NodeIP:NodePort
4. ExternalName：把集群外部的服务引入到集群内部来，在集群内部直接使用。没有任何类型代理被创建，>= 1.7 版本的 kube-dns 才支持