# 使用EKSCTL创建Nodegroup节点组
Nodegroup节点组一共有三种类型
- managed nodegroup
- unmanaged nodegroup
- Fargate

我们的教程选用的是unmanaged(自管理节点)，为什么呢，因为它功能最多，不受限制
标准|EKS管理节点组|自管理节点|AWS Fargate
---|---|---|---
可以运行需要Windows的容器|否|是 – 虽然，您的群集仍然需要至少一个（推荐用于可用性）Linux节点|否
可以运行需要Linux的容器|是|是|是
可以运行需要 推断性芯片|否|是 – 仅Linux节点|否
可以运行需要GPU的工作负载|是 – 仅Linux节点|是 – 仅Linux节点|否
可以运行需要臂处理器的工作负载|否|是 – 预览版本中可用。只能运行Linux节点，节点必须在专用群集中运行|否
POD与其他舱架共享内核运行时环境|是 – 您的每个节点上的所有POD|是 – 您的每个节点上的所有POD|否 – 每个POD都有一个专用内核
POD与其他POD共享CPU、内存、存储和网络资源|是 – 可能导致每个节点上未使用的资源|是 – 可能导致每个节点上未使用的资源|否 – 每个POD都有专用资源，可以独立规模，以最大限度地提高资源利用率
POD可以使用比在POD规格中要求的更多硬件和内存|是 – 如果POD需要比请求的资源更多的资源，而且节点上的资源可用，则POD可以使用其他资源|是 – 如果POD需要比请求的资源更多的资源，而且节点上的资源可用，则POD可以使用其他资源|否，但是可以使用较大的vCPU和存储器配置重新部署POD
必须部署和管理 Amazon EC2 实例|是 – 自动化 Amazon EKS|是 – 手动配置或使用 Amazon EKS-提供 AWS CloudFormation 要部署的模板 Linux(x86)， Linux（ARM），或 窗户 节点|否
必须安全、维护和修补操作系统 Amazon EC2 实例|是|是|否
可以在节点部署时提供引导参数，例如ExtraKubelet参数|否|是 – 有关详细信息，请查看 引导脚本使用信息 在Github上|否 – 没有节点
可以将IP地址从分配给节点的IP地址的不同CIDR数据块分配|否|是|否 – 没有节点
可以通过SSH进入节点|是|是|否 – 没有可用于SSH的节点主机操作系统
可以将自己的Custom-AMI部署到节点|否|是|否 – 您不管理节点
可以将自己的自定义CNI部署到节点|是|是|否 – 您不管理节点
必须自己更新节点AMI|是 –您在 Amazon EKS 当更新可用时，控制台可以在控制台中单击一次执行更新|是 -使用除 Amazon EKS 控制台，因为自管节点无法使用 Amazon EKS 控制台|否 – 您不管理节点
必须自己更新节点Kubernetes版本|是 您在 Amazon EKS 当更新可用并且可以使用鼠标单击更新时，控制台可以执行更新|是 – 使用除 Amazon EKS 控制台，因为自管节点无法使用 Amazon EKS 控制台|否 – 您不管理节点
可以使用 AmazonEBS存储 带有脚架|是|是|否
可以使用 AmazonEFS存储 带有脚架|是|是|否
可以使用 适用于Lustre的AmazonFSX 带POD的存储|是|是|否
可以使用 网络负载均衡器 服务|是|是|否
POD可以在公共子网中运行|是|是|否
可以运行KubernetesDaeminitiate|是|是|否
支持 HostPort 和 HostNetwork 在POD清单中|是|是|否
区域可用性|所有可视区域|所有可视区域|某些 Amazon EKS-支持地区
定价|成本 Amazon EC2 运行多个PODS的实例。有关更多信息，请参阅 Amazon EC2 定价|成本 Amazon EC2 运行多个PODS的实例。有关更多信息，请参阅 Amazon EC2 定价|个人成本 Fargate 内存和CPU配置。每个POD都有自己的成本。有关更多信息，请参阅 AWS Fargate

首先创建一个安全组，设置一些内网的规则，例如ssh端口的开放，记录它的sg-id
定义一个yaml文件
```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: xxx-tokyo-eks-prod-01   ## eks cluster name
  region: ap-northeast-1    ## eks use region

nodeGroups:

  # 系统节点
  - name: system-1-a    ##worker nodegroup名字
    labels: 
        nodetype: system  ## worker 节点的labels
    instanceType: m5.2xlarge   ##计划使用的EC2类型
    minSize: 1      ##autoscaling 最小值
    desiredCapacity: 1  ##autoscaling 常规值
    maxSize: 6 ##autoscaling 最大值
    volumeSize: 100 ##系统系统盘大小
    volumeType: gp2 ##系统盘类型
    availabilityZones: ["ap-northeast-1a"] ##nodegroup所在AZ
    privateNetworking: true ##是否使用私有网络
    securityGroups: ##是否使用自定义安全组
      withShared: true
      withLocal: true
      attachIDs: ["sg-xxxx"]   ##自定义安全组的名字创建一个默认的安全组
    ssh:
      publicKeyName: 'xxxx'  ##可以ssh到worker的key名字
    tags:
      Project: Devops ##定义一个tag，计费使用
      k8s.io/cluster-autoscaler/enabled: "true"   ##定义自动扩容的tag
      k8s.io/cluster-autoscaler/nuclearport-ohio-eks-prod: owned  ##定义自动扩容的tag
    iam:
      withAddonPolicies:    ##选择eks需要使用的iam权限
        #imageBuilder: true
        autoScaler: true
        externalDNS: true
        #certManager: true
        #appMesh: true
        ebs: true
        #fsx: true
        efs: true
        albIngress: true
        #xRay: true
        cloudWatch: true
        
 # worker节点(一个从0开始的弹性集群)
  - name: worker-1-a    ##worker nodegroup名字
    labels:
      nodetype: worker
    instanceType: c5.large   ##计划使用的EC2类型
    minSize: 0      ##autoscaling 最小值
    desiredCapacity: 0  ##autoscaling 常规值
    maxSize: 6 ##autoscaling 最大值
    volumeSize: 100 ##系统系统盘大小
    volumeType: gp2 ##系统盘类型
    availabilityZones: ["ap-northeast-1a"] ##nodegroup所在AZ
    privateNetworking: true
    securityGroups:
      withShared: true
      withLocal: true
      attachIDs: ["sg-xxxx"]
    ssh:
      publicKeyName: 'xxxxx'  ##可以ssh到worker的key名字
    tags:
      Project: Devops ##tag
      k8s.io/cluster-autoscaler/enabled: "true"
      k8s.io/cluster-autoscaler/nuclearport-ohio-eks-prod: owned
      k8s.io/cluster-autoscaler/node-template/label/nodetype: worker
      k8s.io/cluster-autoscaler/node-template/taint/useworker: "true:NoSchedule"
    taints:
      useworker: "true:NoSchedule"
    iam:
      withAddonPolicies:
        #imageBuilder: true
        autoScaler: true
        externalDNS: true
        #certManager: true
        #appMesh: true
        ebs: true
        #fsx: true
        efs: true
        albIngress: true
        #xRay: true
        cloudWatch: true
```
使用命令创建他
```
eksctl create nodegroup --config-file create_nodegroup.yml
```
