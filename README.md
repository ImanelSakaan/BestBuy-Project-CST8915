# BestBuy-Project-CST8915
Lab Project Assignment: Building a Cloud-Native App for Best Buy
## **Scenario**  
Welcome to the Best Buy application.

This application consists of containerized microservices that can be easily deployed into a Kubernetes cluster. This is meant to show a realistic scenario using a polyglot architecture, event-driven design, and common open source back-end services (eg, RabbitMQ, MongoDB). The application also leverages OpenAI's models to generate product descriptions and images. This can be done using either Azure OpenAI or OpenAI.

# 1. Application Architecture Overview

This document outlines the architecture of the Best Buy microservices application.
![image](https://github.com/user-attachments/assets/19422ce3-3682-45bb-a1db-55a0687abf29)

---

## 🌐 Frontend Layer

### 🟦 `store-front` (Vue.js)
- Customer-facing web app.
- Allows users to browse products and place orders.
- Sends order data to the `order-service`.

### 🟩 `store-admin` (Vue.js)
- Used by store staff to manage products and monitor orders.
- Communicates with `product-service` and `makeline-service`.

---

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

---

## 📦 Infrastructure

### 📨 `RabbitMQ`
- Acts as the **order queue** between `order-service` and `makeline-service`.
- Ensures reliable, decoupled message passing.

### 🗃️ `MongoDB`
- Central database for both products and orders.
- Used by multiple services.

---

## 🤖 Simulated Workers

### 🧑‍💻 `virtual-customer` (Rust)
- Simulates customer behavior by placing orders on a schedule.
- Calls the `order-service`.

### 🛠️ `virtual-worker` (Rust)
- Simulates kitchen workers completing orders.
- Interacts with `makeline-service`.

---

## 🔁 Communication Flow

```text
Customer (store-front) 
   ↓
order-service → RabbitMQ → makeline-service
   ↓                           ↓
 MongoDB                 Updates Order Status

store-admin ↔ product-service / makeline-service
virtual-customer → order-service
virtual-worker → makeline-service

```

## 2. Deployment Instructions

### Step 1: Fork and Clone the Repositories

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

### 🐳 Step 2: Containerizing the Algonquin Pet Store Microservices
Here’s your content formatted for GitHub using proper Markdown syntax:

### 🔹 Step 2.1. Dockerize the `order-service`

```bash
cd order-service-L8
```

In the `order-service-L8` directory, create a `Dockerfile` with the following content and copy the appropriate code into it.

**Build the Docker image:**

```bash
docker build -t order-service:latest .
```

**Run a Docker container from `order-service:latest` and expose it on port 3000:**

```bash
docker run --rm -d -p 3000:3000 order-service:latest
```

---

### 🔹 Step 2.2. Dockerize the `product-service`

```bash
cd product-service-L8
```

In the `product-service-L8` directory, create a `Dockerfile` with the appropriate content.

**Build the Docker image:**

```bash
docker build -t product-service:latest .
```

**Run the container on port 3030:**

```bash
docker run --rm -d -p 3030:3030 product-service:latest
```

### 🔹 Step 2.3. Dockerize the `store-front`

```bash
cd ..
cd store-front-L8
```

**Build the Docker image:**

```bash
docker build -t store-front:latest .
```

**Run the container on port 80:**

```bash
docker run --rm -d -p 80:80 store-front:latest
```

### Step 3: Using Docker Compose for Local Development

## Step 3.1. Create a docker-compose.yml file
In the root directory of your project (at the same level as the `order-service-L8`, `product-service`, and `store-front` directories), create a `docker-compose.yml` file:

## Step 3.2. Run the application using Docker Compose
In your terminal, run:
```bash
docker-compose up --build
```
This will build and run all the services together. You should be able to access the store-front at `http://localhost:80`.
```
```


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
