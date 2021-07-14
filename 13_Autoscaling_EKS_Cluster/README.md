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
  - name: scale-west1a
    instanceType: t2.small
    desiredCapacity: 1
    maxSize: 3
    availabilityZones: ["us-west-1a"]
    iam:
      withAddonPolicies:
        autoScaler: true
    labels:
      nodegroup-type: stateful-west1c
      instance-type: onDemand
    ssh:
      enableSsm: true
  - name: scale-west1b
    instanceType: t2.small
    desiredCapacity: 1
    maxSize: 3
    availabilityZones: ["us-west-1b"]
    iam:
      withAddonPolicies:
        autoScaler: true
    labels:
      nodegroup-type: stateful-west1d
      instance-type: onDemand
    ssh:
      enableSsm: true
  - name: scale-spot-1c
    desiredCapacity: 1
    maxSize: 3
    instancesDistribution:
      instanceTypes: ["t2.small", "t3.small"]
      onDemandBaseCapacity: 0
      onDemandPercentageAboveBaseCapacity: 0
    availabilityZones: ["us-west-1c"]
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

## Deploy autoscaler
## Deploy sample app and play with it
