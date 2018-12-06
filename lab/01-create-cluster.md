# Create an AKS Cluster

Create a resource group to hold the cluster. This resource group should be provisioned in West US.

```console
az group create --name AKSPVLab --location westus
```

**Output:**
```
{
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/foo",
  "location": "westus",
  "managedBy": null,
  "name": "AKSPVLab",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Create a two node AKS cluster running Kubernetes 1.11.5:

```console
az aks create -g AKSPVLab --name akspv-1 \
    --kubernetes-version 1.11.5 \
    --node-count 2 \
    --no-ssh-key
```

This cluster create will provision the control plane and attach VMs to your cluster. Initial cluster creation usually takes about 15 minutes.

If you close your Cloud Shell instance, you can watch for cluster creation to complete:

```console
watch az aks list -g AKSPVLab -o table
```

Remember: Press CTRL+C to stop watching.

**Output:**
```
Name      Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
--------  ----------  ---------------  -------------------  -------------------  ------------------------------------------------------
akspv-1   westus      AKSPVLab         1.11.2               Succeeded            akspv-1-AKSPVLab-57ac26-22b0f7a8.hcp.eastus.azmk8s.io
```

## Fetch cluster credentials

Next, we need to fetch credentials to authenticate to the Kubernetes API. We will ask AKS for the cluster admin credentials so that we can configure Resource Based Access Control (RBAC) in a later step.

```console
az aks get-credentials -g AKSPVLab -n akspv-1 --admin
```

**Output:**
```
Merged "akspv-1-admin" as current context in /Users/edvilla/.kube/config
```

## List cluster nodes

Next, let's talk to the new cluster and list the cluster nodes:

```console
kubectl get nodes
```

Note: you will notice that the only nodes you see are your agent nodes; that's because in this case AKS manages the master nodes for you. You can run this same command using ACS-engine, for example, and you will see all the nodes, master and agent.

**Output:**
```
NAME                                         STATUS    ROLES     AGE       VERSION
aks-nodepool1-25718494-0                     Ready     agent     1d        v1.11.5
aks-nodepool1-25718494-1                     Ready     agent     1d        v1.11.5
```

Next [Let's use Shared Storage using Azure Files...](02-azure-files.md)