# Create EKS cluster with one node group

Create file cluster.yaml with the following content
```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: dev-cluster
  region: us-west-2

nodeGroups:
  - name: ng-1-workers
    labels: { role: workers }
    instanceType: t3.small
    desiredCapacity: 1
    volumeSize: 20
    privateNetworking: true
 ```
 
and execute it with:
```
eksctl create cluster -f cluster.yaml
```

Go to AWS console and let's take a look at EC2 panel, you should see your nodes there.
Review Autoscaling group, LaunchTemplate, Ec2 SG and IAM Roles.

ng-1-workes is deployed as unmanaged node group. it is your responsibility to update and patch it.

let's try to update default add-ons installed on nodes

kubeproxy:
```
eksctl utils update-kube-proxy --cluster=dev-cluster --approve
```

aws-node:
```
eksctl utils update-aws-node --cluster=dev-cluster --approve
```

and last one - coredns
```
 eksctl utils update-coredns --cluster=dev-cluster --approve
```

# Let's add another worker group but this time managed with ssm access

edit cluster.yaml with the following content
```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: dev-cluster
  region: us-west-2

nodeGroups:
  - name: ng-1-workers
    labels: { role: workers }
    instanceType: t3.small
    desiredCapacity: 1
    volumeSize: 20
    privateNetworking: true

managedNodeGroups:
  - name: managed-ng-2-workers
    instanceType: t3.micro
    minSize: 1
    desiredCapacity: 1
    maxSize: 4
    labels:
      role: managed-worker
    tags:
      nodegroup-name: managed-ng-1-workers
    privateNetworking: true
    ssh: 
      enableSsm: true
```

and execute it with:
```
eksctl create nodegroup -f cluster.yaml
```

let's see our node groups
```
eksctl get nodegroup --cluster=dev-cluster
```

You can login to the ec2 instance of that group using ec2 connect SSM, let's try to to this form AWS console

Now let's scale out our node group:
```
eksctl scale nodegroup --cluster=dev-cluster --nodes=2 managed-ng-2-workers
```

...and scale in
```
eksctl scale nodegroup --cluster=dev-cluster --nodes=1 managed-ng-2-workers
```

by default nodes are cordoned and pods are evicted from a nodegroup on deletion, but if you need to drain a nodegroup without deleting run:
```
eksctl drain nodegroup --cluster=dev-cluster --name=managed-ng-2-workers
```

then to delete one nodegroups type:
```
eksctl delete nodegroup ng-1-workers --cluster dev-cluster
eksctl delete nodegroup managed-ng-2-workers --cluster dev-cluster
```

# Sometimes it's hard to find what ec2 instantce type do we really need. to make this easier task eksctl integrates with ec2 instance selector. Let's try to deploy nodegroup using that feature.

let's run following command in dry run mode to generate cluster manifest:
```
eksctl create cluster --name dev-cluster --managed --instance-selector-vcpus=2 --instance-selector-memory=4 --dry-run
```

# Spot instances
