# <center>pod<center>
##1.pod的yaml文件模板及属性详解
```yaml
apiVersion: v1        　　          #必选，版本号，例如v1,版本号必须可以用 kubectl api-versions 查询到 .
kind: Pod       　　　　　　         #必选，Pod
metadata:       　　　　　　         #必选，元数据
  name: string        　　          #必选，Pod名称
  namespace: string     　　        #必选，Pod所属的命名空间,默认为"default"
  labels:       　　　　　　         #自定义标签
    - name: string      　          #自定义标签名字
  annotations:        　　          #自定义注释列表
    - name: string
spec:         　　　　　　　         #必选，Pod中容器的详细定义
  containers:                       #必选，Pod中容器列表
  - name: string      　　          #必选，容器名称,需符合RFC 1035规范
    image: string     　　          #必选，容器的镜像名称
    imagePullPolicy: [ Always|Never|IfNotPresent ]  #获取镜像的策略 Alawys表示下载镜像 IfnotPresenIfnotPresentt表示优先使用本地镜像,否则下载镜像，Nerver表示仅使用本地镜像，默认是IfnotPresent
    command: [string]     　　        #容器的启动命令列表，如不指定，使用打包时使用的启动命令
    args: [string]      　　          #容器的启动命令参数列表
    workingDir: string                #容器的工作目录
    volumeMounts:     　　　　        #挂载到容器内部的存储卷配置
    - name: string      　　　        #引用pod定义的共享存储卷的名称，需用volumes[]部分定义的的卷名
      mountPath: string                 #存储卷在容器内mount的绝对路径，应少于512字符
      readOnly: boolean                 #是否为只读模式
    ports:        　　　　　　        #需要暴露的端口库号列表
    - name: string      　　　        #端口的名称
      containerPort: int                #容器需要监听的端口号
      hostPort: int     　　             #容器所在主机需要监听的端口号，默认与Container相同
      protocol: string                  #端口协议，支持TCP和UDP，默认TCP
    env:        　　　　　　            #容器运行前需设置的环境变量列表
    - name: string      　　            #环境变量名称
      value: string     　　            #环境变量的值
    resources:        　　                #资源限制和请求的设置
      limits:       　　　　            #资源限制的设置
        cpu: string     　　            #Cpu的限制，单位为core数，将用于docker run --cpu-shares参数
        memory: string                  #内存限制，单位可以为Mib/Gib，将用于docker run --memory参数
      requests:       　　                #资源请求的设置
        cpu: string     　　            #Cpu请求，容器启动的初始可用数量
        memory: string                    #内存请求,容器启动的初始可用数量
    livenessProbe:      　　            #对Pod内各容器健康检查的设置，当探测无响应几次后将自动重启该容器，检查方法有exec、httpGet和tcpSocket，对一个容器只需设置其中一种方法即可
      exec:       　　　　　　        #对Pod容器内检查方式设置为exec方式
        command: [string]               #exec方式需要制定的命令或脚本
      httpGet:        　　　　        #对Pod内个容器健康检查方法设置为HttpGet，需要制定Path、port
        path: string
        port: number
        host: string
        scheme: string
        HttpHeaders:
        - name: string
          value: string
      tcpSocket:      　　　　　　#对Pod内个容器健康检查方式设置为tcpSocket方式
         port: number
       initialDelaySeconds: 0       #容器启动完成后首次探测的时间，单位为秒
       timeoutSeconds: 0    　　    #对容器健康检查探测等待响应的超时时间，单位秒，默认1秒
       periodSeconds: 0     　　    #对容器监控检查的定期探测时间设置，单位秒，默认10秒一次
       successThreshold: 0
       failureThreshold: 0
       securityContext:
         privileged: false
    restartPolicy: [Always | Never | OnFailure] #Pod的重启策略，Always表示一旦不管以何种方式终止运行，kubelet都将重启，OnFailure表示只有Pod以非0退出码退出才重启，Nerver表示不再重启该Pod
    nodeSelector: obeject   　　    #设置NodeSelector表示将该Pod调度到包含这个label的node上，以key：value的格式指定
    imagePullSecrets:     　　　　#Pull镜像时使用的secret名称，以key：secretkey格式指定
    - name: string
    hostNetwork: false      　　    #是否使用主机网络模式，默认为false，如果设置为true，表示使用宿主机网络
    volumes:        　　　　　　    #在该pod上定义共享存储卷列表
    - name: string     　　 　　    #共享存储卷名称 （volumes类型有很多种）
      emptyDir: {}      　　　　    #类型为emtyDir的存储卷，与Pod同生命周期的一个临时目录。为空值
      hostPath: string      　　    #类型为hostPath的存储卷，表示挂载Pod所在宿主机的目录
        path: string      　　        #Pod所在宿主机的目录，将被用于同期中mount的目录
      secret:       　　　　　　    #类型为secret的存储卷，挂载集群与定义的secre对象到容器内部
        scretname: string  
        items:     
        - key: string
          path: string
      configMap:      　　　　            #类型为configMap的存储卷，挂载预定义的configMap对象到容器内部
        name: string
        items:
        - key: string
          path: string
```
## 2.pod的cheat sheet
![pod cheat sheet](./images/kubernetes-pod-cheatsheet.png)
## 3.pod的生命周期
* pod包含以下几种状态：
   - Pending：API SERVER已经创建pod,但是该pod还有容器没有创建，包括正在下载容器
   - Running：Pod内所有容器都已经创建，且至少有一个容器处于运行、启动或者重启状态
   - Succeeded：Pod内所有容器均成功退出，且不会再重启
   - Failed：Pod内所有容器均已退出，但至少有一个容器退出为失败状态
   - Unknown：由于某种原因无法获取pod的状态，可能由于网络通信不畅。
* pod的重启策略
   - Always: 当容器失效时，有kubelet自动重启改容器
   - OnFailure：当容器终止运行且退出code不为0时，有kubelet自动重启改容器
   - Never：不论容器运行状态如何，kubelet都不会重启改容器
* pod控制器和重启策略关系  
pod的控制器有：ReplicationController、Job、DaemonSet、静态pod
   - RC和DaemonSet：必须为Always，需要保证该容器持续运行
   - Job：OnFailure或Never，确保容器执行完成后不再重启
   - 静态pod：在pod失效时重启，不论RestartPolicy设置为什么值，并且不会对pod进行监控检查
## 4.pod的调度
* ### NodeSelector:定向调度  
可以通过给node设置label，然后再pod的nodeSelector设置相应的属性，具体操作如下
   - 通过kubectl label命令给目标node设置标签  
   $ kubectl label nodes k8s-node-1 zone=north
   - 通过给pode的定义加上nodeSelector如
   ```yaml
      apiVersion: v1
      kind: ReplicationController
      metadata:
        name: redis-master
        labels:
          name: redis-master
      spce:
        replicas: 1
        selector:
          name: redis-master
        template:
          metadata:
            labels:
              name: redis-master
          spec:
            containers:
            - name: master
              images: kubeguide/redis-master
              ports:
              - containerPort: 6379
            nodeSelector:
              zone: north
  ```
  然后运行kubectl create -f来创建pod,scheduler就会将该pod调度到拥有zone=north标签的node上
* ###NodeAffinity：亲和性调度  
  nodeSelector需要通过label来进行精准匹配,所以nodeAffinity增加了In、NotIn、Exists、DoesNotExists、Gt、Lt等操作
## 5.pod的扩容和缩容
- ### 手动指定副本数量  
RC控制的pod，可以通过kubectl scale来修改副本数量  
假定redis-slave的初始副本数量为2，可以通过以下命令改为3   
`` $kubectl scale rc redis-slave --replicas=3  ``  
也可以通过同样地命令，将副本数改为1  
`` $kubectl scale rc redis-slave --replicas=1  ``
- ### 通过cpu使用率自动扩容  
kubelet可以通过Horizontal Pod Autoscaler(HPA)控制器来实现基于CPU利用率的自动扩容  
HPA控制器基于master的kube-controller-manager服务启动参数--horizontal-pod-autoscaler-sync-period定义的时长（默认30秒），周期性检测pod的CPU使用率,并在满足条件时对replicationController或者Deployment中的pod副本数量进行调整，以符合用户定义的平均pod CPU使用率。pod的CPU使用率来源于heapster组件，需要预先安装  
以php-apche为例，先创建pod，指定cpu需求，然后通过autoscale命令来创建hpa
```yaml  
apiVersion: v1
kind: ReplicationController
metadata:
  name: php-apache
spec:
  replicas: 1
  template:
    metadata:
      name: php-apache
      labels:
        app: php-apache
    spce:
      containers:
      - name: php-apache
        images: grc.io/google_continaers/hpa-example
        resources:
          requests:
            cpu: 200m
        ports:
        - containerPort: 80
```
`` $kubectl autoscale rc php-apache --min=1 --max=10 --cpu-percent=50 ``  
或者通过yaml文件
```yaml
kind: HorizontalPodAutoscaler
metadata:
  name: php-apache
spec:
  scaleTargetRef:
    apiVersion: v1
    kind: ReplicationController
    name: php-apache
  minReplicas: 1
  maxReplicas: 10
  targetCPUUtilizationPercentage: 50
```
## 6.pod的滚动升级
- ### 通过yaml文件滚动升级  
编写一个yaml文件，然后通过以下命令来进行滚动升级  
`` kubectl rolling-update redis-master -f redis-master-controller-v2.yaml ``  
其中，yaml文件需要注意以下几点  
   + yaml中的name不能与旧的rc相同
   + 在selector中至少要有一个label与旧的RC的label不同，以标识其为新的RC，
- ###通过kubectl命令滚动升级  
``kubectl rolling-update redis-master --image=redis-master:2.0``
