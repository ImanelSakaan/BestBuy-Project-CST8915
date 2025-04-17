# Information
Here you go — formatted in **GitHub Markdown**:

```markdown
```powershell
PS D:\BestBuyProject\store-front-L8> kubectl version
Client Version: v1.32.2
Kustomize Version: v5.5.0

PS D:\BestBuyProject\store-front-L8> az version
{
  "azure-cli": "2.70.0",
  "azure-cli-core": "2.70.0",
  "azure-cli-telemetry": "1.1.0",
  "extensions": {}
}
```

**[Tenant and subscription selection]**

```text
No     Subscription name    Subscription ID                       Tenant
-----  -------------------  ------------------------------------  -----------------
[1] *  AVD-Prod             cf8bfe92-3d85-4f0a-bbd1-ae319eefc4ee  Algonquin College
[2]    Azure for Students   63b5363b-132e-49e5-969c-286c6a12c8dc  Algonquin College

The default is marked with an *; the default tenant is 'Algonquin College' and subscription is 'AVD-Prod' (cf8bfe92-3d85-4f0a-bbd1-ae319eefc4ee).
```

```powershell
az account set --subscription '63b5363b-132e-49e5-969c-286c6a12c8dc'
PS D:\BestBuyProject\store-front-L8> az aks get-credentials --resource-group BestbuyRG --name BestbuyCluster --overwrite-existing
Merged "BestbuyCluster" as current context in C:\Users\IMAN\.kube\config

kubectl get nodes
NAME                                  STATUS   ROLES    AGE   VERSION
aks-masterpool-38310052-vmss000000    Ready    <none>   52m   v1.31.7
aks-workerspool-38310052-vmss000000   Ready    <none>   52m   v1.31.7
aks-workerspool-38310052-vmss000001   Ready    <none>   52m   v1.31.7
```
Here’s your snippet formatted in **GitHub Markdown**:

```markdown
```powershell
PS D:\BestBuyProject> kubectl apply -f Bestbuy-store-all-in-one.yaml
deployment.apps/rabbitmq created
service/rabbitmq created
deployment.apps/order-service created
service/order-service created
deployment.apps/product-service created
service/product-service created
deployment.apps/store-front created
service/store-front created

PS D:\BestBuyProject> kubectl get nodes
NAME                                  STATUS   ROLES    AGE    VERSION
aks-masterpool-38310052-vmss000000    Ready    <none>   149m   v1.31.7
aks-workerspool-38310052-vmss000000   Ready    <none>   149m   v1.31.7
aks-workerspool-38310052-vmss000001   Ready    <none>   149m   v1.31.7

PS D:\BestBuyProject>
```
```



```

