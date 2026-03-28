# 🔐 Lab 02: Building a Secure 2-Tier Web Application in Azure

**Author:** Hazekiah Kennedy-Wilson  
**Estimated Time:** 60 Minutes  
**Difficulty:** Beginner  

---

## 📌 Lab Overview

In this lab, you will build a classic **Infrastructure as a Service (IaaS)** architecture in Microsoft Azure.

You will create a **Virtual Network (VNet)** with:
- A **Public Subnet** for a Web Server
- A **Private Subnet** for a Database Server

You will also configure **Network Security Groups (NSGs)** to ensure secure communication between resources.

---

## 🎯 Objective

By completing this lab, you will:

- Build a 2-tier cloud architecture  
- Understand public vs private subnet design  
- Deploy and connect multiple virtual machines  
- Secure backend resources using NSGs  

---

## 🧱 Architecture Diagram

```
          Internet
              |
              v
     +------------------+
     |   Public Subnet  |
     |    vm-web-01     |
     +--------+---------+
              |
              v
     +------------------+
     |  Private Subnet  |
     |    vm-db-01      |
     +------------------+
```

---

## 📋 Prerequisites

- [ ] Active Azure Subscription  
- [ ] Terminal installed  

---

## 🏷️ Lab Variables (Naming Convention)

- **Resource Group:**
```
rg-lab02-[yourname] (e.g rg-lab02-hazekiah)
```

- **VNet Name:**
```
vnet-lab02
```

- **Subnet 1 (Public):**
```
snet-web (10.0.1.0/24)
```

- **Subnet 2 (Private):**
```
snet-db (10.0.2.0/24)
```

- **Web VM:**
```
vm-web-01
```

- **Database VM:**
```
vm-db-01
```

---

## 🚀 Step-by-Step Instructions

---

## Phase 1: The Network Foundation

1. Search for **Virtual Networks** → Click **Create**

### Basics:
- Resource Group:
```
rg-lab02-[yourname] (e.g rg-lab02-hazekiah)
```
- Name: `vnet-lab02`  
- Region: `East US`  

### IP Addresses:
- Address Space:
```
10.0.0.0/16
```
- Subnet 1:
```
Name: snet-web  
Range: 10.0.1.0/24
```
- Subnet 2:
```
Name: snet-db  
Range: 10.0.2.0/24
```

Click **Review + create → Create**

---

## Phase 2: Deploying the Web Server (Front End)

1. Search **Virtual Machines** → Click **Create**

### Basics:
- Resource Group:
```
rg-lab02-[yourname] (e.g rg-lab02-hazekiah)
```
- Name: `vm-web-01`  
- Region: `East US`  
- Image: `Ubuntu Server 20.04 LTS`  
- Size: `Standard_B1s`  
- Key pair name: `key-lab02`  
- Public inbound ports: Allow **HTTP (80)** and **SSH (22)**  

### Networking:
- Subnet: `snet-web`  
- Public IP: Create new (Standard)  

Click **Review + create → Create**

Download your `.pem` key if prompted  

---

## Phase 3: Deploying the Database Server (Back End)

1. Create another Virtual Machine  

### Basics:
- Resource Group:
```
rg-lab02-[yourname] (e.g rg-lab02-hazekiah)
```
- Name: `vm-db-01`  
- Region: `East US`  
- Image: `Ubuntu Server 20.04 LTS`  
- Size: `Standard_B1s`  
- Key pair: Use existing `key-lab02`  
- Public inbound ports: Allow **SSH (22)**  

### Networking (CRITICAL):
- Virtual Network: `vnet-lab02`  
- Subnet: `snet-db`  
- Public IP:
```
None
```

Click **Review + create → Create**

---

## Phase 4: Validating Connectivity (Jump Server)

### Step 1: Get DB Private IP
- Go to `vm-db-01`  
- Copy Private IP (example: `10.0.2.4`)  

### Step 2: SSH into Web Server

```bash
ssh -i key-lab02.pem azureuser@[public-ip-of-web]
```

### Step 3: Test Connectivity

```bash
ping 10.0.2.4
```

You should see replies  

Press `Ctrl + C` to stop  

---

## Phase 5: Configure Firewall (NSG)

1. Go to **vm-db-01 → Networking**  
2. Open the **Network Security Group**  
3. Click **Inbound security rules → + Add**

### Create Rule:

- Source:
```
IP Addresses
```
- Source IP:
```
10.0.1.0/24
```
- Source Port:
```
*
```
- Destination:
```
Any
```
- Service:
```
Custom
```
- Destination Port:
```
*
```
- Action:
```
Allow
```
- Priority:
```
100
```
- Name:
```
Allow-Web-Subnet
```

Click **Add**

---

## 🛠️ Troubleshooting

### ❌ Ping Fails
- Ensure vm-db-01 is in `snet-db`  
- Ensure both VMs are in the same VNet  

### ❌ Cannot SSH into DB
- No Public IP assigned  
- Must SSH into Web VM first  

---

## 🧹 Clean Up

Delete Resource Group:

```
rg-lab02-[yourname]
```
