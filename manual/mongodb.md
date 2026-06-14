# MongoDB Setup

## What is MongoDB
NoSQL database used to store product catalogue data.

## Instance Details
- AMI: ami-0220d79f3f480ecf5
- Type: t2.micro
- Region: us-east-1 N.Virginia
- Name: mongodb

## Connection
- Tool: MobaXterm
- User: ec2-user
- Switch to root: sudo su -

## Setup Commands
# MongoDB Setup

## What is MongoDB
NoSQL database used to store product catalogue data.

## Instance Details
- AMI: ami-0220d79f3f480ecf5
- Type: t2.micro
- Region: us-east-1 N.Virginia
- Name: mongodb

## Connection
- Tool: MobaXterm
- User: ec2-user
- Switch to root: `sudo su -`

## Setup Commands

**Step 1 - Setup MongoDB repo file**
```
vim /etc/yum.repos.d/mongo.repo
```

**Add this content:**
```
[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/9/mongodb-org/7.0/x86_64/
enabled=1
gpgcheck=0
```

**Step 2 - Install MongoDB**
```
dnf install mongodb-org -y
```

**Step 3 - Start and Enable Service**
```
systemctl enable mongod
systemctl start mongod
```

**Step 4 - Update Config**
```
vim /etc/mongod.conf
```
Change: `127.0.0.1` → `0.0.0.0`

**Step 5 - Restart Service**
```
systemctl restart mongod
```

## Status Check
```
systemctl status mongod
```

## Why Change 127.0.0.1 to 0.0.0.0?
MongoDB by default only allows localhost access.
Changing to 0.0.0.0 allows Catalogue EC2 to connect.

## Port
- 27017
## Status Check
systemctl status mongod
→ Active: running ✅

## Important Config Change
File: /etc/mongod.conf
Changed: 127.0.0.1 → 0.0.0.0
Reason: Allow other EC2 instances to connect

## Port
27017
