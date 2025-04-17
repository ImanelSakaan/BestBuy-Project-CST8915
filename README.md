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

### 🐳 Step 2: Containerizing the Algonquin Pet Store Microservices
Here’s your content formatted for GitHub using proper Markdown syntax:

### 🔹 Step 2.1. Dockerize the `order-service`

```bash
cd order-service
```

In the `order-service` directory, create a `Dockerfile` with the following content and copy the appropriate code into it.

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

You can choose between the **Python** or **Rust** implementation of the `product-service`.

#### 👉 Python Version

```bash
cd product-service-python-L4
```

In the `product-service-python-L4` directory, create a `Dockerfile` with the appropriate content.

**Build the Docker image:**

```bash
docker build -t product-service-python:latest .
```

**Run the container on port 3030:**

```bash
docker run --rm -d -p 3030:3030 product-service-python:latest
```

#### 👉 Rust Version

```bash
cd product-service-rust-L4
```

In the `product-service-rust-L4` directory, create a `Dockerfile` with the appropriate content.

**Build the Docker image:**

```bash
docker build -t product-service-rust:latest .
```

**Run the container on port 3030:**

```bash
docker run --rm -d -p 3030:3030 product-service-rust:latest
```

---

### 🔹 Step 2.3. Dockerize the `store-front`

```bash
cd ..
cd store-front-L4
```

**Build the Docker image:**

```bash
docker build -t store-front:latest .
```

**Run the container on port 80:**

```bash
docker run --rm -d -p 80:80 store-front:latest
```
```

Let me know if you'd like the rest of the services formatted in the same way!
