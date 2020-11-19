创建集群
```
eksctl create clustername
```
从配置文件创建节点组
```
eksctl create nodegroup --config-file=dev-cluster.yaml
```
扩容缩容命令
```
eksctl scale nodegroup --cluster=<clusterName> --nodes=<desiredCount> --name=<nodegroupName> [ --nodes-min=<minSize> ] [ --nodes-max=<maxSize> ]
eksctl scale nodegroup --cluster=cluster-1 --nodes=5 ng-a345f4e1
```
更新标签
```
kubectl label nodes -l alpha.eksctl.io/nodegroup-name=ng-1 new-label=foo
```
排空节点(删除之前需要做的
```
eksctl drain nodegroup --cluster=<clusterName> --name=<nodegroupName>
```
删除节点
```
eksctl delete nodegroup --cluster=<clusterName> --name=<nodegroupName>
```