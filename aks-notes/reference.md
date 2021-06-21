# Using Azure Kubernetes Service

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