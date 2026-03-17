# 🚀 Azure Cloud Migration with Blue-Green Deployment

## 📌 Project Overview

This project demonstrates a **real-world DevOps workflow** by migrating a containerized application from an **on-premise server (Virtual Machine)** to **Azure Cloud**, and implementing **Blue-Green Deployment** using Azure App Service deployment slots.

---

## 🎯 Objectives

* Simulate an **on-prem server using VirtualBox**
* Containerize application using **Docker**
* Perform **cloud migration to Azure App Service**
* Implement **zero-downtime deployment (Blue-Green strategy)**
* Showcase **end-to-end DevOps pipeline**

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

## 📸 Screenshots

Include the following:

1. Local App Running
2. Docker Container Running
3. VM Server Deployment
4. Azure App Service
5. Deployment Slots (Blue & Green)
6. Staging URL (New Version)
7. Slot Swap (Before & After)

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

## 🏆 Key Achievements

* Simulated real **on-prem to cloud migration**
* Implemented **Blue-Green deployment**
* Achieved **zero downtime release**
* Used **Azure production-grade services**

---


## 👨‍💻 Author

**Nirmalya Das**

---
