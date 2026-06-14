# Catalogue Service

## What is Catalogue
Microservice responsible for serving the list of items 
displayed in Roboshop application.

## Technology
- Runtime: NodeJS 20
- Developed by: Internal Dev Team

## Instance Details
- AMI: ami-0220d79f3f480ecf5
- Type: t2.micro
- Region: us-east-1 N.Virginia
- Name: catalogue

## Connection
- Tool: MobaXterm
- User: ec2-user
- Switch to root: `sudo su -`

## Setup Commands

**Step 1 - Install NodeJS 20**
```bash
dnf module disable nodejs -y
dnf module enable nodejs:20 -y
dnf install nodejs -y
```

**Step 2 - Create Roboshop System User**
```bash
useradd --system --home /app \
--shell /sbin/nologin \
--comment "roboshop system user" roboshop
```
> Roboshop is a system/daemon user — not for login

**Step 3 - Create App Directory**
```bash
mkdir /app
```

**Step 4 - Download Application Code**
```bash
curl -o /tmp/catalogue.zip \
https://roboshop-artifacts.s3.amazonaws.com/catalogue-v3.zip
cd /app
unzip /tmp/catalogue.zip
```

**Step 5 - Install Dependencies**
```bash
cd /app
npm install
```

**Step 6 - Create Catalogue Service**
```bash
vim /etc/systemd/system/catalogue.service
```

Add this content:
```
[Unit]
Description=Catalogue Service

[Service]
User=roboshop
Environment=MONGO=true
Environment=MONGO_URL="mongodb://<MONGODB-IP>:27017/catalogue"
ExecStart=/bin/node /app/server.js
SyslogIdentifier=catalogue

[Install]
WantedBy=multi-user.target
```
> ⚠️ Replace `<MONGODB-IP>` with your MongoDB EC2 Private IP!

**Step 7 - Start Catalogue Service**
```bash
systemctl daemon-reload
systemctl enable catalogue
systemctl start catalogue
```

**Step 8 - Setup MongoDB Repo on Catalogue Server**
```bash
vim /etc/yum.repos.d/mongo.repo
```

Add this content:
```
[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/9/mongodb-org/7.0/x86_64/
enabled=1
gpgcheck=0
```

**Step 9 - Install MongoDB Client**
```bash
dnf install mongodb-mongosh -y
```

**Step 10 - Load Master Data**
```bash
mongosh --host <MONGODB-IP> </app/db/master-data.js
```
> ⚠️ Replace `<MONGODB-IP>` with your MongoDB EC2 Private IP!

## Verify Data Loaded Successfully
```bash
# Connect to MongoDB
mongosh --host <MONGODB-IP>

# Show databases
show dbs

# Use catalogue database
use catalogue

# Show collections
show collections

# Get products
db.products.find()
```

## Important Notes
- Update MongoDB IP in catalogue.service file
- Update Catalogue IP in Frontend nginx.conf
- Port: 27017 (MongoDB)
- Catalogue runs as roboshop user — not root

## Status Check
```bash
systemctl status catalogue
```
