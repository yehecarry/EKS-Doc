# 使用EKSCTL创建EKS集群 
使用命令创建一个新的EKS集群
```
[ec2-user@ip-172-31-36-214 ~]$ eksctl create cluster --name eks-ohio-test  --without-nodegroup --vpc-cidr 10.230.0.0/16 --region us-east-2
[ℹ]  eksctl version 0.26.0
[ℹ]  using region us-east-2
[ℹ]  setting availability zones to [us-east-2c us-east-2a us-east-2b]
[ℹ]  subnets for us-east-2c - public:10.230.0.0/19 private:10.230.96.0/19
[ℹ]  subnets for us-east-2a - public:10.230.32.0/19 private:10.230.128.0/19
[ℹ]  subnets for us-east-2b - public:10.230.64.0/19 private:10.230.160.0/19
[ℹ]  using Kubernetes version 1.17
[ℹ]  creating EKS cluster "eks-ohio-test" in "us-east-2" region with 
[ℹ]  if you encounter any issues, check CloudFormation console or try 'eksctl utils describe-stacks --region=us-east-2 --cluster=eks-ohio-test'
[ℹ]  CloudWatch logging will not be enabled for cluster "eks-ohio-test" in "us-east-2"
[ℹ]  you can enable it with 'eksctl utils update-cluster-logging --region=us-east-2 --cluster=eks-ohio-test'
[ℹ]  Kubernetes API endpoint access will use default of {publicAccess=true, privateAccess=false} for cluster "eks-ohio-test" in "us-east-2"
[ℹ]  2 sequential tasks: { create cluster control plane "eks-ohio-test", no tasks }
[ℹ]  building cluster stack "eksctl-eks-ohio-test-cluster"
[ℹ]  deploying stack "eksctl-eks-ohio-test-cluster"
```