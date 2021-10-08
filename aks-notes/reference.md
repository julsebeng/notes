Using Azure Kubernetes Service

## Useful commands
- List all available AKS resources:
  ```bash
  az aks list -o table
  ```
- Get credentials for a particular AKS cluster:
  ```bash
  az aks get-credentials -n <AKS name> -g <Resource Group>
  ```
  This will load the corresponding credentials into `kubectl` and switch contexts automatically.
- Managing contexts:
  ```bash
  kubectl config get-contexts # Get all contexts that have been used
  kubectl use-context <name of context> # Switch context
  ```
- Start/stop a cluster:
  ```bash
  az aks stop --name <Cluster Name> --resource-group <Resource Group>
  az aks start --name <Cluster Name> --resource-group <Resource Group>
  ```
- Get the node resource group for an AKS cluster:
  ```bash
  az aks show --name <Cluster Name> --resource-group <Resource group> --query  nodeResourceGroup -o tsv
  ```
  - When a new AKS cluster is created, an additional resource group is automatically created to house the Kubernetes 
    infrastructure. Resources like public IPs must be in this resource group, and not in the resource group the Kubernetes
    service was created in.
- Create a public IP inside of a node resource group:
  ```bash
  az network public-ip --resource-group <Node Resource Group>  \
                       --name <Name for new IP resource> \
                       --sku Standard \
                       --allocation-method static \
                       --query publicIp.ipAddress -o tsv
  ```
  - The query isn't required, but it's useful to display the created public IP address.