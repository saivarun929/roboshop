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
Setup the MongoDB repo file

[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/9/mongodb-org/7.0/x86_64/
enabled=1
gpgcheck=0
Hint! You can create file by using vim /etc/yum.repos.d/mongo.repo

Install MongoDB

dnf install mongodb-org -y 
Start & Enable MongoDB Service

systemctl enable mongod 
systemctl start mongod 
Usually MongoDB opens the port only to localhost(127.0.0.1), meaning this service can be accessed by the application that is hosted on this server only. However, we need to access this service to be accessed by another server, So we need to change the config accordingly.

Update listen address from 127.0.0.1 to 0.0.0.0 in /etc/mongod.conf

You can edit file by using vim /etc/mongod.conf
## Status Check
systemctl status mongod
→ Active: running ✅

## Important Config Change
File: /etc/mongod.conf
Changed: 127.0.0.1 → 0.0.0.0
Reason: Allow other EC2 instances to connect

## Port
27017
