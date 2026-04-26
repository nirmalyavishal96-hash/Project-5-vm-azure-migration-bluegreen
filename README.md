# Production-Ready Azure VM Migration with Blue-Green Deployment Strategy

## 📌 Problem Statement

Modern applications require continuous deployment with minimal downtime, but traditional deployment methods often lead to:

- Service interruptions during updates  
- Increased risk of deployment failures  
- Difficulty in rolling back faulty releases  
- Lack of separation between staging and production environments  

These challenges make it difficult to ensure reliable and seamless application delivery.

---

## Solution

This project implements a **Blue-Green deployment strategy on Azure App Service** to achieve zero-downtime releases.

- Migrated a containerized application from an on-premise virtual machine to Azure Cloud  
- Used Azure App Service deployment slots (Blue = Production, Green = Staging)  
- Deployed new versions to the staging environment before switching traffic  
- Performed instant traffic swap to release updates without downtime  
- Enabled quick rollback by reversing slot swap in case of failure  
---

## Impact 
- Achieved zero-downtime deployment using Blue-Green strategy  
- Reduced deployment risk by enabling instant rollback via slot swap  
- Improved release reliability by separating staging and production environments

---

## 🏗️ Architecture

### 🔹 Before Migration (On-Prem)

```
Local Machine
    ↓
Virtual Machine (Ubuntu Server)
    ↓
Docker Container (Flask App)
```

---

### 🔹 After Migration (Azure Cloud)

```
User
  ↓
Azure App Service
  ↓
 ├── Production Slot (Blue)
 └── Staging Slot (Green)
```

---

## 🛠️ Tech Stack

* **Cloud**: Azure App Service
* **Containerization**: Docker
* **Backend**: Flask (Python)
* **Server**: Ubuntu (VirtualBox VM)
* **CI/CD Concept**: Blue-Green Deployment
* **CLI Tools**: Azure CLI

---

## 🚀 Project Workflow

### 1️⃣ Local Development

* Built a simple **Task Manager Web App**
* Implemented CRUD operations
* Ran application on `localhost`

---

### 2️⃣ Containerization

```bash
docker build -t task-app .
docker run -p 5000:5000 task-app
```

---

### 3️⃣ On-Prem Deployment (VM)

* Created Ubuntu Server using VirtualBox
* Installed Docker
* Deployed app inside VM

```bash
docker pull <dockerhub-username>/task-app
docker run -d -p 5000:5000 <dockerhub-username>/task-app
```

Access:

```
http://192.168.56.xxx:5000
```

---

### 4️⃣ Cloud Migration (Azure)

```bash
az group create --name devops-rg --location centralindia

az appservice plan create \
  --name devops-plan \
  --resource-group devops-rg \
  --sku B1 \
  --is-linux

az webapp create \
  --resource-group devops-rg \
  --plan devops-plan \
  --name <app-name> \
  --deployment-container-image-name <dockerhub-username>/task-app:latest
```

---

### 5️⃣ Blue-Green Deployment

#### Create Slot

```bash
az webapp deployment slot create \
  --name <app-name> \
  --resource-group devops-rg \
  --slot staging
```

#### Deploy to Staging

```bash
az webapp config container set \
  --name <app-name> \
  --resource-group devops-rg \
  --slot staging \
  --docker-custom-image-name <dockerhub-username>/task-app:latest
```

#### Swap Slots

```bash
az webapp deployment slot swap \
  --name <app-name> \
  --resource-group devops-rg \
  --slot staging
```

---

## 🔄 Blue-Green Deployment Flow

```
Production (Blue) → Live Users
Staging (Green) → New Version

Swap → Zero Downtime Deployment
```

---

## 🔁 Reliability & Rollback Strategy

- Deployment is first tested in staging slot (Green)  
- Production traffic is switched only after validation  
- In case of failure, instant rollback is achieved by swapping slots back  
- Ensures zero downtime and minimal user impact

---

## 🌍 Real-World Use Case

This project simulates enterprise deployment strategies where applications must be updated without downtime, ensuring continuous availability for users.

---

## 📸 Screenshots

Attached:

1. Docker Container Running
2. VM Server Deployment
3. Azure App Service
4. Deployment Slots (Blue & Green)
5. Deployment URL (Version-1)
6. Staging URL (Version-2)
7. Slot Swap Deployment URL (Version-2)

---

## 🐞 Debugging & Issues Faced

### ❌ Docker Permission Issue

```
permission denied docker.sock
```

✅ Fix:

```bash
sudo usermod -aG docker $USER
```

---

### ❌ Port Already Allocated

```
Bind for 0.0.0.0:5000 failed
```

✅ Fix:

```bash
docker ps
docker stop <container_id>
```

---

### ❌ VM Network Not Working

* `enp0s8` not getting IP

✅ Fix:

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
sudo netplan apply
```

---

### ❌ Azure Slot Not Available

```
Basic plan does not support slots
```

✅ Fix:

```bash
az appservice plan update --sku S1
```

---

## 💰 Cost Optimization

* Used **B1 plan initially**
* Upgraded to **S1 only for deployment slots**
* Deleted resources after testing:

```bash
az group delete --name devops-rg --yes --no-wait
```

---


## 👨‍💻 Author

**Nirmalya Das**

DevOps Engineer | Cloud | Automation

---
