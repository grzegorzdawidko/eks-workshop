# Autoscaling EKS Cluster
## Prepare EKS
cluster.yaml
```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: dev-cluster
  region: us-west-2

nodeGroups:
  - name: scale-west2b
    instanceType: t2.small
    desiredCapacity: 1
    maxSize: 3
    availabilityZones: ["us-west-2b"]
    iam:
      withAddonPolicies:
        autoScaler: true
    labels:
      nodegroup-type: stateful-west1c
      instance-type: onDemand
    ssh:
      enableSsm: true
  - name: scale-west2c
    instanceType: t2.small
    desiredCapacity: 1
    maxSize: 3
    availabilityZones: ["us-west-2c"]
    iam:
      withAddonPolicies:
        autoScaler: true
    labels:
      nodegroup-type: stateful-west2c
      instance-type: onDemand
    ssh:
      enableSsm: true
  - name: scale-spot-2d
    desiredCapacity: 1
    maxSize: 3
    instancesDistribution:
      instanceTypes: ["t2.small", "t3.small"]
      onDemandBaseCapacity: 0
      onDemandPercentageAboveBaseCapacity: 0
    availabilityZones: ["us-west-2d"]
    iam:
      withAddonPolicies:
        autoScaler: true
    labels:
      nodegroup-type: stateless-workload
      instance-type: spot
    ssh: 
      enableSsm: true
```

Deploy cluster or update nodegroups if it already exist
```
ekscrl create cluster -f cluster.yaml
```

update kube config
```
aws eks update-kubeconfig --name dev-cluster
```

test
```
kubectl get pod -A
```
## Deploy autoscaler

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml
```
  
put required annotation to the autoscaler deployment:

```
kubectl -n kube-system annotate deployment.apps/cluster-autoscaler cluster-autoscaler.kubernetes.io/safe-to-evict="false"
```

check kubernetes version:
```
kubectl version
```

edit deployment and set your EKS cluster name:

```
kubectl -n kube-system edit deployment.apps/cluster-autoscaler
```

Open https://github.com/kubernetes/autoscaler/releases and search latest match realise for your cluster version.

For Kubernetes 1.19 use https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.19.1

=> set the image version at property ```image=k8s.gcr.io/cluster-autoscaler:vx.yy.z``` for example 1.19.1

=> set your EKS cluster name at the end of property ```- --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/<<EKS cluster name>>``` for example dev-cluster

Check status:
```
kubectl get deployment -A
```

## Deploy sample app and play with it
