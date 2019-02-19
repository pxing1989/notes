# <center>Controller Manager
Controller Manager由kube-controller-manager和cloud-controller-manager组成，是Kubernetes的大脑，它通过apiserver监控整个集群的状态，并确保集群处于预期的工作状态。
kube-controller-manager由一系列的控制器组成  
- Replication Controller
- Node Controller
- CronJob Controller
- Daemon Controller
- Deployment Controller
- Endpoint Controller
- Garbage Collector
- Namespace Controller
- Job Controller
- Pod AutoScaler
- RelicaSet
- Service Controller
- ServiceAccount Controller
- StatefulSet Controller
- Volume Controller
- Resource quota Controller
## Replication Controller
Replication Controller的核心作用是确保在任何时候集群中有一个RC所关联的pod副本数量保持预设值。需要注意的是，只有在pod的重启策略是Always的时候(RestartPolicy=Always),Replication Controller才会管理pod的操作  
Replication Controller职责：
- 确保当前集群有且仅有N个Pod的实例；
- 通过调整RC的spec.replicas属性来实现系统的扩容或者缩容
- 通过改变RC的pod模板来实现系统的滚动升级
## Node Controller
