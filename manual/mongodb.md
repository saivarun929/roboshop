# MongoDB Setup

## What is MongoDB
NoSQL database used to store product catalogue data.

## Instance Details
- Type: t2.micro
- OS: RHEL 9
- Region: ap-south-1 Mumbai

## Setup Steps
1. Create repo file - /etc/yum.repos.d/mongo.repo
2. Install MongoDB - dnf install mongodb-org -y
3. Enable service - systemctl enable mongod
4. Start service - systemctl start mongod
5. Update config - change 127.0.0.1 to 0.0.0.0
6. Restart service - systemctl restart mongod

## Port
- MongoDB runs on port 27017
