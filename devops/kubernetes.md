# Kubernetes

### Keywords
kubernetes minikube kubectl devops
#

## Setup Overview
Kubernetes can be used either in the cloud **(Google GKE, Amazon EKS)** or locally **(Minikube)**.

## Local Setup

### MiniKube
Start a **local cluster** using minikube
```
minikube start
```

Delete the cluster
```
minikube delete
```
#
### EKS Setup

Setup `eksctl`
```
brew tap weaveworks/tap
```
```
brew install weaveworks/tap/eksctl
```

Create a cluster
```
eksctl create cluster \
 --name <cluster-name> \
 --region <region> \
 --without-nodegroup
```

Get Amazon account IAM info
```
aws sts get-caller-identity
```

Configure kubeconfig with EKS
```
aws eks --region <region> \
    update-kubeconfig --name <cluster_name>
```

Delete EKS cluster
```

```

#
### GKE Setup

#
## Kubectl
Communication with the kubernetes engine is either done through **GUI** or a **CLI** tool called `kubectl`.

### Quick Deployment

Quick create deployment
```
create deployment hello-minikube\ 
    --image=k8s.gcr.io/echoserver:1.10
```

Quick expose deployment
```
kubectl expose deployment hello-minikube\
    --type=NodePort --port=8080
```

Quick create service
```
minikube service hello-minikube --url
```

Check pods
```
kubectl get pods
```

Delete
```
kubectl delete services hello-minikube
kubectl delete deployment hello-minikube
```

#
### Detailed Deployment

create a namespace
```
kubectl create namespace <namespace>
```

create a deployment from file
```
kubectl apply -f <deployment.yaml> -n <namespace>
```

create a service from file
```
kubectl apply -f <service.yaml> -n <namespace>
```

OR

expose the deployment
```
kubectl expose deployment <deployment_name> \
    --port <port>   \
    --type="LoadBalancer"
```