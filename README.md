# 本文是在企业使用EKS1.16集群的教程教程大纲如下
## 部署EKS集群
### 在EC2中部署EKS管理节点
#### 安装kubectl
#### 安装eksctl
#### 安装helm3
### 集群中创建EKS Cluster集群
### 创建EKS Nodegroup节点组
## 部署服务插件
### 外网暴露
#### ALB
#### NLB
### 持久化存储
#### EBS
#### EFS
### 弹性扩容
#### Autoscaler
#### VPA
#### HPA
### 集群认证
#### 创建kubeconfig
#### EC2如何管理EKS集群
### 集群监控
#### 部署prometheus Operator
#### 部署thanos上传至S3
### 日志收集
#### 在EKS中使用ELK
#### 在EKS中使用Loki
### 服务网格
#### EKS与App Mesh
#### EKS与Istio
### CI/CD
#### 使用Helm部署Jenkins
#### 使用Helm管理Jenkins动态slave
### 其他服务
#### kubernetes dashboard
#### CoreDNS
#### NACOS配置中心
## 使用EKS集群
### 在EKS集群部署JAVA服务案例
### 在EKS使用服务的注意事项