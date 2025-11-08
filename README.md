# 🏗️ AWS RDS Three-Tier Architecture Documentation

**Author:** Sudarshan Mane  
**Project:** AWS RDS Three-Tier Architecture Setup  
**Tools Used:** AWS Management Console, PowerShell CLI  
**Purpose:** To create a secure, scalable web application architecture using EC2, RDS, and VPC  

---

## 📘 Overview

The Three-Tier Architecture divides the system into three main layers:

1. **Presentation Tier (Frontend)**
2. **Application Tier (Backend)**
3. **Database Tier (Amazon RDS)**

This approach improves security, scalability, and fault tolerance in cloud applications.

---

## 🌐 1️⃣ Presentation Tier – Frontend

**Role:**  
Handles user interface and interactions.

**Technologies:**  
- HTML, CSS, Php

**Hosting Options:**  
- EC2 Instance (Web Server)

### ⚙️ PowerShell Commands
```powershell
# Launch EC2 instance for Frontend
aws ec2 run-instances `
  --image-id ami-0abcd1234efgh5678 `
  --count 1 `
  --instance-type t2.micro `
  --key-name mykeypair `
  --security-group-ids sg-frontend `
  --subnet-id subnet-public `
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=FrontendServer}]'

# Connect to EC2 instance
ssh -i "mykeypair.pem" ec2-user@<EC2-Public-IP>

# Inside EC2 (Linux commands)
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
```

---

## ⚙️ 2️⃣ Application Tier – Backend

**Role:**  
Processes business logic and communicates with the database.

**Technologies:**  
- Java Spring Boot, Node.js, Python Flask

**Deployment:**  
- EC2 instance in a Private Subnet

### ⚙️ PowerShell Commands
```powershell
# Launch EC2 instance for Backend
aws ec2 run-instances `
  --image-id ami-0abcd1234efgh5678 `
  --count 1 `
  --instance-type t2.micro `
  --key-name mykeypair `
  --security-group-ids sg-backend `
  --subnet-id subnet-private `
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=BackendServer}]'

# Example API call from frontend
Invoke-RestMethod -Uri "http://<Backend-Private-IP>:8080/api/test" -Method GET
```

**🔒 Security Rule:**  
Allow only traffic from Frontend Security Group (`sg-frontend`) to Backend Security Group (`sg-backend`).

---

## 💾 3️⃣ Database Tier – RDS

**Role:**  
Stores application data securely using Amazon RDS.

**Engine Options:**  
- MySQL / MariaDB / PostgreSQL

**Deployment:**  
- Private Subnet (No public access)

### ⚙️ PowerShell Commands
```powershell
# Create RDS instance
aws rds create-db-instance `
  --db-instance-identifier myrdsdb `
  --db-instance-class db.t3.micro `
  --engine mysql `
  --master-username admin `
  --master-user-password mypassword `
  --allocated-storage 20 `
  --vpc-security-group-ids sg-database `
  --db-subnet-group-name mydbsubnetgroup

# Connect Backend EC2 to RDS
mysql -h myrdsdb.xxxxxxxx.ap-south-1.rds.amazonaws.com -u admin -p
```

**🔒 Security Rule:**  
Allow only traffic from Backend Security Group (`sg-backend`) to Database Security Group (`sg-database`).

---

## 🔁 Data Flow Diagram

```
[User] 🌐 → [Frontend EC2 / S3] → [Backend EC2] → [Amazon RDS Database]
```

---

## 🧠 Benefits

- ✅ High Security using private/public subnets
- ✅ Scalable Application Layers
- ✅ AWS Managed Database (RDS)
- ✅ Cost Efficient and Easy to Maintain

---

## 📂 File Information

**File Name:** rds-three-tier-architecture.md  
**Format:** Markdown (.md)  
**Contains:** Documentation + PowerShell commands  
**Author:** Sudarshan Mane  
