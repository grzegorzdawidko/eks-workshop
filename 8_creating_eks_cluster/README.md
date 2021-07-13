# Create EKS cluster with eksctl
copy content

```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: basic-cluster
  region: us-west-2
```

into cluster.yaml

```
vi cluster.yaml
```


type ``:q!`` to quit vi(m)

```
eksctl create cluster -f cluster.yaml
```

you should see:

```
[ℹ]  eksctl version 0.20.0
[ℹ]  using region us-west-2
[ℹ]  setting availability zones to [us-west-2a us-west-2b us-west-2c]
[ℹ]  subnets for us-west-2a - public:192.168.0.0/19 private:192.168.96.0/19
[ℹ]  subnets for us-west-2b - public:192.168.32.0/19 private:192.168.128.0/19
[ℹ]  subnets for us-west-2c - public:192.168.64.0/19 private:192.168.160.0/19
[ℹ]  using Kubernetes version 1.16
[ℹ]  creating EKS cluster "basic-cluster" in "us-west-2" region with 
[ℹ]  will create a CloudFormation stack for cluster itself and 0 nodegroup stack(s)
[ℹ]  will create a CloudFormation stack for cluster itself and 0 managed nodegroup stack(s)
[ℹ]  if you encounter any issues, check CloudFormation console or try 'eksctl utils describe-stacks --region=us-west-2 --cluster=basic-cluster'
[ℹ]  CloudWatch logging will not be enabled for cluster "basic-cluster" in "us-west-2"
[ℹ]  you can enable it with 'eksctl utils update-cluster-logging --region=us-west-2 --cluster=basic-cluster'
[ℹ]  Kubernetes API endpoint access will use default of {publicAccess=true, privateAccess=false} for cluster "basic-cluster" in "us-west-2"
[ℹ]  2 sequential tasks: { create cluster control plane "basic-cluster", no tasks }
[ℹ]  building cluster stack "eksctl-basic-cluster-cluster"
[ℹ]  deploying stack "eksctl-basic-cluster-cluster"
......
......
[ℹ]  deploying stack "eksctl-basic-cluster-cluster"
[ℹ]  waiting for the control plane availability...
[✔]  saved kubeconfig as "/Users/username/.kube/config"
[ℹ]  no tasks
[✔]  all EKS cluster resources for "basic-cluster" have been created
[ℹ]  kubectl command should work with "/Users/username/.kube/config", try 'kubectl get nodes'
[✔]  EKS cluster "basic-cluster" in "us-west-2" region is ready
```

login into aws console and let's take a look at the Cloudformation service in us-west-2

when creation is done
review following resources VPC, IAM, EKS, EC2 Security groups

N

