# GitOps Demo
This is used by the [Introduction to GitOps](https://cloudacademy.com/course/introduction-gitops/) course presented by CloudAcademy.

![GitOps Demo](./docs/GitOps1.png)

**Updates**
- Fri 4 Dec 2020: updated install instructions to work with latest versions of tools and Flux chart

## Fork Repo
If you intend to watch the course and repeat the same instructions in your own environment, then you *must* fork this repository into your own GitHub account. The reason for this, is that you need to be the *owner* of the repo to be able to upload and configure a new *Deploy Key* within the *Settings* area of the repo. The new *Deploy Key* will contain the Flux operators SSH public key. 

## GitOps - Flux Install Instructions 

### TOOLS (versions)

- helm (v3.4.0)
- kubectl (1.19.3)
- minikube (1.15.1)
- k8s (v1.19.4)

**NOTE**: Helm3 is easier and more secure - doesn't require the Tiller component/service to be installed in the K8s cluster (Helm2 did)

### Step 1.

Start a K8s cluster locally - only do this if you need a cluster

```
minikube start --memory=4g --kubernetes-version=v1.19.4
```

### Step 2.

Use browser and open https://artifacthub.io/

Search for "Flux" - click on the "flux" chart result - here you can review the install instructions, which follow.

Run the following commands to install the "flux" chart

```
helm repo add flux https://charts.fluxcd.io
helm repo update
```

```
kubectl create ns flux
kubectl create ns cloudacademy
```

```
helm search repo flux
```

**NOTE**: replace the git.url parameter with YOUR FORKed copy - so that you can later set the SSH public key as a DeployKey within your own Github FORKed repo

```
helm install flux --set git.url=git@github.com:cloudacademy/gitops-demo --namespace flux flux/flux --version 1.6.0
```

**NOTE**: if needed you can perform a "helm upgrade" to change the Flux deployed chart's git.url like this:

```
helm upgrade -i flux fluxcd/flux --set git.url=git@github.com:myaccount/gitops-demo --namespace flux
```

Examine the rollout of Flux...

```
kubectl rollout status deployment flux -n flux
```

```
kubectl get pods -n flux
```

```
kubectl -n flux logs deployment/flux --follow
```

CTRL-C to exit previous command

### Step 3.

Retrieve SSH public key and then add as a DeployKey within your own Github FORKed repo:

```
kubectl -n flux logs deployment/flux | grep identity.pub | cut -d '"' -f2
```

### Step 4.

Check to see that the gitops-demo resources have been automatically deployed by Flux into the cloudacademy namespace within the K8s cluster

```
kubectl get pods -n cloudacademy
kubectl describe pod -n cloudacademy
```

```
kubectl get pods -n cloudacademy --watch
```

CTRL-C to exit previous command

```
kubectl rollout status deployment frontend -n cloudacademy
```