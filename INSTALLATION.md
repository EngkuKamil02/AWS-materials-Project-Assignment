# Installation Guide – Cloud Photo Gallery (AWS)

This document provides a **complete step-by-step guide** to deploy the *Cloud Photo Gallery* web application using **AWS Free Tier** services. Follow all steps in order to successfully replicate the project.

---

## Prerequisites

Before starting, ensure you have:
- AWS Free Tier account
- GitHub account
- Basic Linux knowledge
- SSH client (PuTTY / Terminal / CMD)
- WinSCP or FileZilla
- Web browser (Chrome / Firefox)

---

## Step 1: Create VPC

1. Go to **AWS Console → VPC → Create VPC**
2. Choose **VPC only**
3. Configure:
   - Name: `PhotoGallery-VPC`
   - IPv4 CIDR: `10.0.0.0/16`
4. Click **Create VPC**

---

## Step 2: Create Subnets

Create **private subnets** in different Availability Zones:

### Subnet 1
- Name: `PhotoGallery-Private-1A`
- AZ: `ap-southeast-1a`
- CIDR: `10.0.3.0/24`

### Subnet 2
- Name: `PhotoGallery-Private-1B`
- AZ: `ap-southeast-1b`
- CIDR: `10.0.4.0/24`

---

## Step 3: Create Internet Gateway

1. Go to **VPC → Internet Gateways**
2. Click **Create internet gateway**
3. Name: `PhotoGallery-IGW`
4. Attach it to `PhotoGallery-VPC`

---

## Step 4: Create Route Table

1. Go to **Route Tables → Create route table**
2. Name: `PhotoGallery-RT`
3. Add route:
   - Destination: `0.0.0.0/0`
   - Target: Internet Gateway
4. Associate with public subnet (if used)

---

## Step 5: Create Security Groups

### Security Group 1 – Web Server
- Name: `PhotoGallery-Web-SG`
- Inbound rules:
  - HTTP (80) – Anywhere
  - SSH (22) – My IP

### Security Group 2 – Database
- Name: `PhotoGallery-DB-SG`
- Inbound rules:
  - MySQL (3306) – Source: Web Server SG

---

## Step 6: S3 Storage Setup

### Create S3 Bucket
1. Go to **S3 → Create bucket**
2. Bucket name: `cloud-photo-gallery-<unique>`
3. Disable "Block all public access"
4. Create bucket

### Create Folder Structure
- `uploads/`
- `thumbnails/`

### Set Bucket Policy
Allow public read access for images.

### Enable CORS
Allow PUT, GET, POST methods for uploads.

---

## Step 7: RDS Database Setup

### Create DB Subnet Group
1. Go to **RDS → Subnet groups**
2. Create subnet group using:
   - `PhotoGallery-Private-1A`
   - `PhotoGallery-Private-1B`

### Create RDS Instance
- Engine: MySQL
- Instance type: `db.t3.micro`
- Username & Password: **SAVE THESE**
- VPC: `PhotoGallery-VPC`
- Security Group: `PhotoGallery-DB-SG`

📌 **Important:**
- Copy the **RDS endpoint**
- Remember database username and password

---

## Step 8: IAM Role Setup

1. Create IAM Role for EC2
2. Attach policy:
   - `AmazonS3FullAccess`
3. Assign role to EC2 instance

---

## Step 9: EC2 Instance Setup

1. Launch EC2 instance
2. OS: **Ubuntu 22.04 LTS**
3. Instance type: `t2.micro`
4. Security group: `PhotoGallery-Web-SG`
5. Key pair: create & download

---

## Step 10: Connect to EC2 Instance

```bash
ssh -i key.pem ubuntu@<EC2-Public-IP>
```

---

## Step 11: Install Required Packages

### Update system
```bash
sudo apt update
```

### Install Apache & PHP
```bash
sudo apt install apache2 php php-mysql -y
```

---

## Step 12: Install AWS CLI

```bash
sudo apt install awscli -y
```

---

## Step 13: Install Composer

```bash
sudo apt install composer -y
```

---

## Step 14: Install AWS SDK for PHP

```bash
composer require aws/aws-sdk-php
```

---

## Step 15: Configure Apache

```bash
sudo systemctl restart apache2
```

---

## Step 16: Set Proper Permissions

```bash
sudo chown -R ubuntu:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

---

## Step 17: Upload Application Files

Use **WinSCP** to upload project files to:
```
/var/www/html
```

---

## Step 18: Configure `config.php`

Update the following:
- RDS endpoint
- Database username
- Database password
- S3 bucket name

---

## Step 19: Setup Database

Open browser:
```
http://<EC2-Public-IP>/setup_database.php
```

After success:
- Delete `setup_database.php`

---

## Step 20: Test the Website

- Open browser
- Visit:
```
http://<EC2-Public-IP>
```
- Register user
- login
- dashboard
- Upload photo
- View gallery
- log out

---

## Completion 🎉

The Cloud Photo Gallery web application is now successfully deployed using AWS Free Tier services.

---

## Disclaimer

This project is developed for academic purposes under the **IIB43203 Cloud Computing** course using AWS Free Tier resources.

