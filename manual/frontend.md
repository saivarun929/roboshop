
# Frontend Service

## What is Frontend
Static web content service for Roboshop application.
Served using Nginx web server.

## Technology
- Web Server: Nginx 1.24
- Content: Static HTML/CSS/JS

## Instance Details
- AMI: ami-0220d79f3f480ecf5
- Type: t2.micro
- Region: us-east-1 N.Virginia
- Name: frontend

## Connection
- Tool: MobaXterm
- User: ec2-user
- Switch to root: `sudo su -`

## Setup Commands

**Step 1 - Install Nginx**
```bash
dnf module disable nginx -y
dnf module enable nginx:1.24 -y
dnf install nginx -y
```

**Step 2 - Start and Enable Nginx**
```bash
systemctl enable nginx
systemctl start nginx
```
> ✅ Test: Access EC2 Public IP in browser
> Should show Nginx default page

**Step 3 - Remove Default Content**
```bash
rm -rf /usr/share/nginx/html/*
```

**Step 4 - Download Frontend Content**
```bash
curl -o /tmp/frontend.zip \
https://roboshop-artifacts.s3.amazonaws.com/frontend-v3.zip
```

**Step 5 - Extract Frontend Content**
```bash
cd /usr/share/nginx/html
unzip /tmp/frontend.zip
```
> ✅ Test: Access EC2 Public IP in browser
> Should show Roboshop page now

**Step 6 - Configure Nginx Reverse Proxy**
```bash
vim /etc/nginx/nginx.conf
```

Replace entire content with:
```nginx
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log notice;
pid /run/nginx.pid;

include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    keepalive_timeout   65;
    types_hash_max_size 4096;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80;
        listen       [::]:80;
        server_name  _;
        root         /usr/share/nginx/html;

        include /etc/nginx/default.d/*.conf;

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }

        location /images/ {
          expires 5s;
          root   /usr/share/nginx/html;
          try_files $uri /images/placeholder.jpg;
        }

        location /api/catalogue/ { 
          proxy_pass http://catalogue.devopssai.online:8080/; 
        }
        location /api/user/ { 
          proxy_pass http://user.devopssai.online:8080/; 
        }
        location /api/cart/ { 
          proxy_pass http://cart.devopssai.online:8080/; 
        }
        location /api/shipping/ { 
          proxy_pass http://shipping.devopssai.online:8080/; 
        }
        location /api/payment/ { 
          proxy_pass http://payment.devopssai.online:8080/; 
        }

        location /health {
          stub_status on;
          access_log off;
        }
    }
}
```

**Step 7 - Restart Nginx**
```bash
systemctl restart nginx
```

## Status Check
```bash
systemctl status nginx
```

## Important Notes
- Replace localhost with actual DNS names of each component
- DNS pattern → componentname.devopssai.online
- Frontend runs on port 80
- All API calls proxied to backend services

## Access
- URL: http://devopssai.online
- Port: 80 (HTTP)
- Note: HTTPS not configured yet

## DNS Records Needed in Route 53
| Component | DNS Name |
|-----------|----------|
| MongoDB | mongodb.devopssai.online |
| Catalogue | catalogue.devopssai.online |
| User | user.devopssai.online |
| Cart | cart.devopssai.online |
| Shipping | shipping.devopssai.online |
| Payment | payment.devopssai.online |

## Port Reference
| Service | Port |
|---------|------|
| Nginx Frontend | 80 |
| Catalogue | 8080 |
| User | 8080 |
| Cart | 8080 |
| Shipping | 8080 |
| Payment | 8080 |
