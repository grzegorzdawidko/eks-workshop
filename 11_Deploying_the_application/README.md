# Deploying the application


## Prepare cluster

Create EKS cluster or add node group to the existing one

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
    minSize: 1
    desiredCapacity: 1
    maxSize: 4
    volumeSize: 20
    privateNetworking: true
 ```
 
and execute it with:
```
eksctl create [cluster|nodegroup] -f cluster.yaml
```
## Create Pod

Update kube config:
```
aws eks update-kubeconfig --name dev-cluster
```
Check cluster
```
kubectl cluster-info
```

...much more info here:
```
kubectl cluster-info dump
```

Check kluster nodes:
```
 kubectl get nodes
```

List all pods in all namespaces:
```
kubectl get pods -A
```

Let's create simple pod from our python app, update <account_id> with your account id:

pod.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: static-web
  labels:
    role: myrole
spec:
  containers:
    - name: web
      image: <account_id>.dkr.ecr.us-west-2.amazonaws.com/repo1:latest
      ports:
        - name: web
          containerPort: 8001
          protocol: TCP
```

Create namespace test
```
kubectl create ns test
```

Deploy pod in the namespace test:
```
kubectl apply -f pod.yaml -n test
```
See the pod:
```
kubectl get pod -n test
```

Check the logs:
```
kubectl logs -f static-web -n test
```

Check IP and Node IP assigned by CNI plugin. Check EC2 instance network section from AWS console
```
kubectl get pod -n test -owide
```

Now let's delete pod and a namespace:

pod:
```
kubectl delete pod static-web -n test
```

ns:
```
kubectl delete ns test
```

## Create Deployment
## Create Service
