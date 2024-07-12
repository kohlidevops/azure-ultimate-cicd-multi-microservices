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
