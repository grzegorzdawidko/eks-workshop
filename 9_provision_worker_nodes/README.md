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

to delete one nodegroup type:
```
eksctl delete nodegroup managed-ng-2-workers --cluster dev-cluster
```
