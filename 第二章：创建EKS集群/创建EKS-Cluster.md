# 使用EKSCTL创建EKS集群 
使用命令创建一个新的EKS集群
```
[ec2-user@ip-172-31-36-214 ~]$ eksctl create cluster --name eks-ohio-test  --without-nodegroup --vpc-cidr 10.230.0.0/16 --region us-east-2 --version 1.16
[ℹ]  eksctl version 0.26.0
[ℹ]  using region us-east-2
[ℹ]  setting availability zones to [us-east-2a us-east-2c us-east-2b]
[ℹ]  subnets for us-east-2a - public:10.230.0.0/19 private:10.230.96.0/19
[ℹ]  subnets for us-east-2c - public:10.230.32.0/19 private:10.230.128.0/19
[ℹ]  subnets for us-east-2b - public:10.230.64.0/19 private:10.230.160.0/19
[ℹ]  using Kubernetes version 1.16
[ℹ]  creating EKS cluster "eks-ohio-test" in "us-east-2" region with 
[ℹ]  if you encounter any issues, check CloudFormation console or try 'eksctl utils describe-stacks --region=us-east-2 --cluster=eks-ohio-test'
[ℹ]  CloudWatch logging will not be enabled for cluster "eks-ohio-test" in "us-east-2"
[ℹ]  you can enable it with 'eksctl utils update-cluster-logging --region=us-east-2 --cluster=eks-ohio-test'
[ℹ]  Kubernetes API endpoint access will use default of {publicAccess=true, privateAccess=false} for cluster "eks-ohio-test" in "us-east-2"
[ℹ]  2 sequential tasks: { create cluster control plane "eks-ohio-test", no tasks }
[ℹ]  building cluster stack "eksctl-eks-ohio-test-cluster"
[ℹ]  deploying stack "eksctl-eks-ohio-test-cluster"
[ℹ]  waiting for the control plane availability...
[✔]  saved kubeconfig as "/home/ec2-user/.kube/config"
[ℹ]  no tasks
[✔]  all EKS cluster resources for "eks-ohio-test" have been created
[ℹ]  kubectl command should work with "/home/ec2-user/.kube/config", try 'kubectl get nodes'
[✔]  EKS cluster "eks-ohio-test" in "us-east-2" region is ready
[✔]  EKS cluster "eks-ohio-test" in "us-east-2" region is ready
```
可以查看kube-system namespace中的pod
```
[ec2-user@ip-172-31-36-214 ~]$ kubectl  get all -n kube-system
NAME                           READY   STATUS    RESTARTS   AGE
pod/coredns-67bfd975c5-9rxnz   0/1     Pending   0          4m33s
pod/coredns-67bfd975c5-jt5zq   0/1     Pending   0          4m33s

NAME               TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)         AGE
service/kube-dns   ClusterIP   172.20.0.10   <none>        53/UDP,53/TCP   4m39s

NAME                        DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/aws-node     0         0         0       0            0           <none>          4m39s
daemonset.apps/kube-proxy   0         0         0       0            0           <none>          4m39s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/coredns   0/2     2            0           4m39s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/coredns-67bfd975c5   2  aaSzxx       2         0       4m33s
```
## EKSCTL都创建了什么
EKSCTL首先创建了一个Cloudfortion(以下简称CF)，它调用这个CF创建了一个有六个subnet的VPC，其中三个subnet为Public网络，三个subnet为Private网络  
为Private网络打上kubernetes.io/role/internal-elb:1 的tag
为Pubilc网络打上kubernetes.io/role/elb:1 的tag
为Private网络添加三个Nat网关
然后创建一个EKS Cluster的资源