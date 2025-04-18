# BestBuy-Project-CST8915
Lab Project Assignment: Building a Cloud-Native App for Best Buy
## **Scenario**  
Welcome to the Best Buy application.

This application consists of containerized microservices that can be easily deployed into a Kubernetes cluster. This is meant to show a realistic scenario using a polyglot architecture, event-driven design, and common open source back-end services (eg, RabbitMQ, MongoDB). The application also leverages OpenAI's models to generate product descriptions and images. This can be done using either Azure OpenAI or OpenAI.
### 📹 Demo Video

[Watch the Demo](2025-04-18 12-47-50.mp4)


# 1. Application Architecture Overview
## Architecture

The application has the following services: 

| Service | Description | Github Repo |
| --- | --- | --- |
| `store-front` | Web app for customers to place orders (Vue.js) | [store-front-L8](https://github.com/ImanelSakaan/store-front-L8) |
| `store-admin` | Web app used by store employees to view orders in queue and manage products (Vue.js) | [store-admin-L8](https://github.com/ImanelSakaan/store-admin-L8) |
| `order-service` | This service is used for placing orders (Javascript) | [order-service-L8](https://github.com/ImanelSakaan/order-service-L8) |
| `product-service` | This service is used to perform CRUD operations on products (Rust) | [product-service-L8](https://github.com/ImanelSakaan/product-service-L8) |
| `makeline-service` | This service handles processing orders from the queue and completing them (Golang) | [makeline-service-L8](https://github.com/ImanelSakaan/makeline-service-L8) |
| `ai-service` | Optional service for adding generative text and graphics creation (Python) | [ai-service-L8](https://github.com/ImanelSakaan/ai-service-L8) |
| `rabbitmq` | RabbitMQ for an order queue | [rabbitmq](https://github.com/docker-library/rabbitmq) |
| `mongodb` | MongoDB instance for persisted data | [mongodb](https://github.com/docker-library/mongo) |
| `virtual-customer` | Simulates order creation on a scheduled basis (Rust) | [virtual-customer-L8](https://github.com/ImanelSakaan/virtual-customer-L8) |
| `virtual-worker` | Simulates order completion on a scheduled basis (Rust) | [virtual-worker-L8](https://github.com/ImanelSakaan/virtual-worker-L8) |



This document outlines the architecture of the Best Buy microservices application.
![image](https://github.com/user-attachments/assets/81dbb957-03dd-4555-9e0e-27e00be75226)


## 🌐 Frontend Layer

### 🟦 `store-front` (Vue.js)
- Customer-facing web app.
- Allows users to browse products and place orders.
- Sends order data to the `order-service`.

### 🟩 `store-admin` (Vue.js)
- Used by store staff to manage products and monitor orders.
- Communicates with `product-service` and `makeline-service`.

## ⚙️ Backend Services

### 🔵 `order-service` (JavaScript)
- Receives new orders from `store-front`.
- Publishes orders to **RabbitMQ**.
- Persists order data in `MongoDB`.

### 🟤 `product-service` (Rust)
- Provides CRUD operations for product management.
- Stores product data in `MongoDB`.

### 🟠 `makeline-service` (Golang)
- Subscribes to RabbitMQ queue.
- Processes and completes orders.
- Updates order status in `MongoDB`.

### 🧠 `ai-service` (Python, Optional)
- Optional generative AI features (e.g., image generation, text descriptions).
- Can be used by other services or frontend apps.

## 📦 Infrastructure

### 📨 `RabbitMQ`
- replaced it with Azure Service Bus.
- Ensures reliable, decoupled message passing.

### 🗃️ `MongoDB`
- Central database for both products and orders.
- Used by multiple services.

## 🤖 Simulated Workers

### 🧑‍💻 `virtual-customer` 
- Simulates customer behavior by placing orders on a schedule.
- Calls the `order-service`.

### 🛠️ `virtual-worker` 
- Simulates kitchen workers completing orders.
- Interacts with `makeline-service`.

# 2. 🔁 Deployment Instructions

### 🌐 Step 1: Fork and Clone the Repositories

```bash
git clone https://github.com/ImanelSakaan/store-front-L8.git
git clone https://github.com/shaoxian423/store-admin-L8.git
git clone https://github.com/ImanelSakaan/order-service-L8.git
git clone https://github.com/ramymohamed10/product-service-L8.git
git clone https://github.com/shaoxian423/makeline-service-L8.git
git clone https://github.com/ImanelSakaan/ai-service-L8.git
git clone https://github.com/ImanelSakaan/rabbitmq.git
git clone https://github.com/ImanelSakaan/mongo.git
git clone https://github.com/ImanelSakaan/virtual-customer-L8.git
git clone https://github.com/ImanelSakaan/virtual-worker-L8.git
```
...

### 🐳Step 2: Create an Azure Kubernetes Cluster (AKS)
1. **Log in to Azure Portal:**
   - Go to [https://portal.azure.com](https://portal.azure.com) and log in with your Azure account.

2. **Create a Resource Group:**
   - In the Azure Portal, search for **Resource Groups** in the search bar.
   - Click **Create** and fill in the following:
     - **Resource group name**: `BestbuyRG`
     - **Region**: `Canada Central`.
   - Click **Review + Create** and then **Create**.
  
3. **Create an AKS Cluster:**
   - In the search bar, type **Kubernetes services** and click on it.
   - Click **Create** and select **Kubernetes cluster**
   - In the `Basics` tap fill in the following details:
     - **Subscription**: Select your subscription.
     - **Resource group**: Choose `BestbuyRG`.
     - **Cluster preset configuration**: Choose `Dev/Test`.
     - **Kubernetes cluster name**: `BestbuyCluster`.
     - **Region**: Same as your resource group (e.g., `Canada Central`).
     - **Availability zones**: `None`.
     - **AKS pricing tier**: `Free`.
     - **Kubernetes version**: `Default`.
     - **Automatic upgrade**: `Disabled`.
     - **Automatic upgrade scheduler**: `No schedule`.
     - **Node security channel type**: `None`.
     - **Security channel scheduler**: `No schedule`.
     - **Authentication and Authorization**: `Local accounts with Kubernetes RBAC`.
   - In the `Node pools` tap fill in the following details:
     - Select **agentpool**. Optionally change its name to `masterpool`. This nodes will have the controlplane.
        - Set **node size** to `D2as_v4`.
        - **Scale method**: `Manual`
        - **Node count**: `1`
        - Click `update`
     - Click on **Add node pool**:
        - **Node pool name**: `workerspool`.
        - **Mode**: `User` 
        - Set **node size** to `D2as_v5`.
        - **Scale method**: `Manual`
        - **Node count**: `1`
        - Click `add`
   - Click **Review + Create**, and then **Create**. The deployment will take a few minutes.
     
   4. **Connect to the AKS Cluster:**
   - Once the AKS cluster is deployed, navigate to the cluster in the Azure Portal.
   - In the overview page, click on **Connect**. 
   - Select **Azure CLI** tap. You will need Azure CLI. If you don't have it: [**Install Azure CLI**](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
   - Login to your azure account using the following command:
      ```
      az login
      ```
   - Set the cluster subscription using the command shown in the portal (it will look something like this):
      ```
      az account set --subscription 'subscribtion-id'
      ```

   - Copy the command shown in the portal for configuring `kubectl` (it will look something like this):
     ```
     az aks get-credentials --resource-group AlgonquinPetStoreRG --name AlgonquinPetStoreCluster
     ```
      **Understanding the Command:**
      - The command `az aks get-credentials` pulls the necessary configuration files to enable `kubectl` to access your AKS cluster. Here’s a breakdown:
     - `--resource-group` specifies the resource group where your AKS cluster resides.
     - `--name` specifies the name of your AKS cluster.
     - `--overwrite-existing` can be used to overwrite any existing Kubernetes configuration files for the same cluster. This is useful if you’ve connected to the cluster before or if 
           multiple configurations exist for it.
      - Verify Cluster Access:
      - Test your connection to the AKS cluster by listing all nodes:
        ```
        kubectl get nodes
        ```
        NAME                                  STATUS   ROLES    AGE   VERSION
         aks-masterpool-38310052-vmss000000    Ready    <none>   52m   v1.31.7
         aks-workerspool-38310052-vmss000000   Ready    <none>   52m   v1.31.7
         aks-workerspool-38310052-vmss000001   Ready    <none>   52m   v1.31.7

 
## 🐳Step 3: Create Resources Using kubectl and YAML
   This section will show you how to create kubernetes resources using kubectl command and YAML file.
   
   ### Create an Azure Kubernetes Cluster:
   Create an AKS cluster with one worker node for this exercise.
   
   ### Create a Pod:
   - Using **`YAML`**
   
    - Using **`YAML`**
      - Create a file **`my-deployment.yaml`** with the following content:
   ``` yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: my-deployment
   spec:
     replicas: 3
     selector:
       matchLabels:
         app: nginx
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx
           ports:
           - containerPort: 80
```

## 🟠Step 4: Deploy the Best Buy Application

1. **Apply the YAML file to the AKS cluster:**
   - In this step, use the K8s deployment YAML file provided: `algonquin-pet-store-all-in-one.yaml`.
   - Open the terminal and navigate to the file directory.
   - Run the following command to apply the YAML configuration and deploy the application to AKS:
      ```
      kubectl apply -f Bestbuy-store-all-in-one.yaml
      ```

2. **Verify the deployment:**
   - After the command executes, verify that the pods are running by using the following command:
     ```bash
     kubectl get pods
     ```

3. **Check services:**
   - Confirm that all services are up and running:
     ```bash
     kubectl get services
     ```

4. **Access the Store Front Application:**
   - The **Store Front** service is configured as a LoadBalancer, which exposes the application to the internet.
   - In the Azure Portal, go to **Kubernetes Services** > **AlgonquinPetStoreCluster** > **Services and ingresses**.
   - Locate the **store-front** service, and note the **EXTERNAL-IP** address.

5. **Test the Store Front:**
   - Open a web browser and enter the external IP address to access the Store Front (You may need to wait about one minute).

6. **Verifying the backend services**:
   - `order-service` is accessible at:
      ```
      http://<EXTERNAL-IP>/orders
      ``` 
   - `product-service` is accessible at:
      ```
      http://<EXTERNAL-IP>/products
      ``` 
   - `RabbitMQ Management Dashboard` is accessible at:
      ```
      http://<EXTERNAL-IP>/rabbitmq
      ``` 
      - Use the following credentials to log in:
        - Username: myuser
        - Password: mypassword
        - 

## 🟠 Step 5: Set Up the AI Backing Services     
   1. **Create an Azure OpenAI Service Instance**

   - Search for **Azure OpenAI** in the Azure Marketplace.

   - **Set Up the Azure OpenAI Resource**:
     - Choose the **East US** region for deployment to ensure capacity for **GPT-4** and **DALL·E 3** models.
     - Fill in the required details:
       - **Resource group**: Use an existing one or create a new group.
       - **Pricing tier**: Select **Standard**.

   - **Deploy the Resource**:
     - Click **Review + Create**, then click **Create** to deploy the Azure OpenAI service.

   2. **Deploy the GPT-4 and DALL·E 3 Models**

   - **Access the Azure OpenAI Resource**:
     - Navigate to the Azure OpenAI resource you just created.

   - **Deploy GPT-4**:
     - Go to the **Model Deployments** section and click **Add Deployment**.
     - Choose **GPT-4** as the model and provide a deployment name (e.g., `gpt-4-deployment`).
     - Set the deployment configuration as required and deploy the model.

   - **Deploy DALL·E 3**:
     - Repeat the same process to deploy **DALL·E 3**.
     - Use a descriptive deployment name (e.g., `dalle-3-deployment`).

   - **Note Configuration Details**:
     Once deployed, make sure to note down the following details for each model:
     - **Deployment Name**
     - **Endpoint URL**

...

# 3. Deployment Instructions
