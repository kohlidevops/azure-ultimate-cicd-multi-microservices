# azure-ultimate-cicd-multi-microservices

**To create an AKS cluster**

Login to portal.azure.com and choose AKS services - Create a Kubernetes cluster

```
Subscription - Azure subscription 1
Resource group - demo
Cluster preset configuration - Dev/Test
Kubernetes cluster name - azuredevops
Region - Asia Pacific South India
Availability zones -  None //No availability zones are available
AKS pricing tier - Free
Kubernetes version - 1.28.9
Automatic upgrade - Enabled with patch
Automatic upgrade scheduler - Every week on Sunday
Automatic upgrade scheduler - Node Image
Security channel scheduler - Every week on Sunday
Authentication and Authorization - Local accouints with Kubernetes RBAC
choose Next
```
<img width="797" alt="image" src="https://github.com/user-attachments/assets/09302652-b2e6-4b81-9e82-2d01533ce97b">

**Configure Node Pools**

```
Select your agentpool
Node pool name - agentpool
Mode - System
OS SKU - Ubuntu Linux
Availability zones -  None
Node size - Standard D2s v3
Scale method - Autoscale
Minimum node count - 1
Maximum node count - 2
Max pods per node - 30
Enable public IP per node - yes
update
```

<img width="762" alt="image" src="https://github.com/user-attachments/assets/ba512c7e-2a2a-40c5-8ea4-cc57b93ea1fc">

I leave others as default and Review + Create a Kubernetes Cluster.

Kubernetes Cluster has been created successfully on my Azure portal.

<img width="944" alt="image" src="https://github.com/user-attachments/assets/a6132afa-8cc7-4953-8589-f9cff986acdc">

**Login to Kubernetes Cluster**

Just open the Azure Cloud shell and operate from here!

```
az aks get-credentials --name azuredevops --overwrite-existing --resource-group demo
```

Lets check with nodes and pods

```
kubectl get nodes
kubectl get pods
```

<img width="938" alt="image" src="https://github.com/user-attachments/assets/9706386a-ae3a-4eb3-96ae-bb55ff5d0493">

**Install ArgoCD on the Kubernets Cluster**

Create a namespace and install the argocd

https://github.com/kohlidevops/azure-ultimate-cicd-multi-microservices/edit/main/README.md

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

<img width="893" alt="image" src="https://github.com/user-attachments/assets/b0847d0d-3e42-4898-86a2-bdece58dfbd8">

To check the ArgoCD pods whether it is running

```
kubectl get pods -n argocd
```

All the pods are up and running

<img width="670" alt="image" src="https://github.com/user-attachments/assets/e1561f30-1677-4eaa-812c-3c32a4a6a698">

**To configure a ArgoCD**

First we need to get the initial admin password to login the ArgoCD

```
kubectl get secrets -n argocd
kubectl edit secret argocd-initial-admin-secret -n argocd
```

<img width="536" alt="image" src="https://github.com/user-attachments/assets/70a7e2bc-e0f6-48cb-8dfe-da197187890b">

To copy the password which is encoded. We have to decode the password using base64

```
echo <encoded-copied-secrets> | base64 --decode
echo AABBCCDDEEFFGGHH | base64 --decode
```

Once you decode, you get the real password to login to the ArgoCD.

ArgoCD username - admin
password - vIy0mkyWJz1AxJN4

To access the ArgoCD

By default, argocd comes with cluster IP which is not accessible in browser. We need to change the Type from ClusterIP to NodePort

<img width="818" alt="image" src="https://github.com/user-attachments/assets/a27ddd2c-7fb4-444e-b2b0-6fa9e065df67">

```
kubectl get svc -n argocd
kubectl edit svc argocd-server -n argocd

type: NodePort

save and exit
kubectl get svc -n argocd
```

<img width="818" alt="image" src="https://github.com/user-attachments/assets/68f3abe6-f611-487f-9258-f943b9d6c6a6">

Now you can access the argocd - Because we have changed the type as NodePort and we have to find the Node Ip to access.

```
kubectl get nodes -o wide
```

<img width="949" alt="image" src="https://github.com/user-attachments/assets/78b45037-1db1-46a1-ab15-0937d2ca8eab">

Pls white-list the node instance security group with argocd port number - Azure - VMSS - node instance - networking - Add inbound port - add it and save.

Now try to access the argocd and login.

<img width="941" alt="image" src="https://github.com/user-attachments/assets/8a38eb45-899a-45f8-9848-f6aa89d8c9eb">

You can use below link to import the repo to the Azure repo

To import the voting app to the azure repo

Login to the dev.azure.com and create a new project called ultimatecicd - Then select a Azure repo and choose import the repo using below link.

```
https://github.com/dockersamples/example-voting-app.git
```

<img width="943" alt="image" src="https://github.com/user-attachments/assets/a493d1eb-b26c-4194-b5b8-7e4e5a73238a">

After import the repo you can see - However, Azure repo is secure private repo, So If argocd need a access if they want to access the repo. For this, we have to create PAT token.

<img width="935" alt="image" src="https://github.com/user-attachments/assets/f548bda0-d9bc-48a7-814c-22036c9cc7e9">

Copy this token for later use - 

Go back to Argocd login - settings - connect repo

```
connection method - HTTPS
Type - git
Project - default
Repositry URL - https://myorganization1109@dev.azure.com/myorganization1109/ultimatecicd/_git/ultimatecicd
// replace myorganization1109 with your PAT token
// https://AAAAAAAAAAAAAAAAAAAAAAAAAAAA@dev.azure.com/myorganization1109/ultimatecicd/_git/ultimatecicd
Connect now
```

Its connected!

<img width="777" alt="image" src="https://github.com/user-attachments/assets/a094daf9-e7f1-4d9e-813a-0564d96fc353">

To Create a new application in ArgoCD

Login to Argocd and create new app

```
Application Name - voteapp-service
Project Name - default
Sync Policy - Automatic
Repository URL - https://AAAAAAAAAAAAAAAAAAAAAA@dev.azure.com/myorganization1109/ultimatecicd/_git/ultimatecicd
Revision - HEAD
Path - k8s-specifications   //The argocd should keep eye on this directory of Azure repo
Destination Cluster URL - https://kubernetes.default.svc
Namespace - default
Then create
```

<img width="923" alt="image" src="https://github.com/user-attachments/assets/6711a546-e2ca-4465-ad95-bdacff479679">








