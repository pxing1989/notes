# <center>service
## 1.service的yaml文件模板及属性详解
```yaml
apiVersion: v1       #指定api版本，此值必须在kubectl apiversion中
kind: Service         #指定创建资源的角色/类型
metadata:               #资源的元数据/属性
  name: string #资源的名字，在同一个namespace中必须唯一
  namespace: string     #必选，Pod所属的命名空间,默认为"default"
  labels: #设定资源的标签，详情请
    - name: string      　#自定义标签名字
  annotations:            #自定义注解列表
    - name: string        #自定义注解名字
spec:
  selector: []     #Label selector配置，将选择具有指定Label标签的pod作为管理范围
  type: string     #service的类型，默认为clusterIP。
                #clusterIP：虚拟ip地址，kubernetes集群内部pod访问；
                #NodePort：使用宿主机端口；
                #LoadBalancer：使用外接负载均衡器完成到服务器的负载分发
  clusterIP: string
  sessionAffinity: string   #是否支持session，默认为空，可选值为ClientIP，
  ports:                 #service需要暴露的端口列表
  - name: string         #端口名称
    protocol: string      #协议，支持TCP和UDP，默认为TCP
    port: int             #服务端监听的端口
    targetPort: int       #需要转发到pod的端口
    nodePort: int         #spec.type=NodePort时，指定映射到物理机的端口
  status:   #spec.type=LoadBalancer时，设置外部负载均衡器地址，用于公有云
    loadBalancer:  
      ingress:
        ip: string     #外部负载均衡器ip
        hostname: string      #外部负载均衡器主机名
```
