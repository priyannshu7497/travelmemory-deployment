<div align="center">

# 🌍 Travel Memory App  
### MERN Stack | AWS EC2 Deployment | Cloudflare | PM2 | NGINX

---

![Status](https://img.shields.io/badge/Status-Deployed-brightgreen)
![License](https://img.shields.io/badge/License-MIT-blue)
![Made With](https://img.shields.io/badge/Made%20With-MERN%20Stack-orange)
![Cloud](https://img.shields.io/badge/Hosted%20On-AWS%20EC2-yellow)
![SSL](https://img.shields.io/badge/Secured%20With-Cloudflare%20SSL-purple)

</div>

---

## 📚 Table of Contents

| Section |
|--------|
| 1. Project Overview |
| 2. Student Information |
| 3. Objectives |
| 4. Tech Stack |
| 5. Setup & Deployment |
| 6. Scaling and Load Balancing |
| 7. Cloudflare Configuration |
| 8. Testing |
| 9. Screenshots |
| 10. Architecture Diagram |
| 11. Final Deliverables |
| 12. Repository Link |
| 13. Future Improvements |
| 14. Credits |

---

## 👤 Student Information

| Field | Details |
|-------|---------|
| **Name** | Priyanshu Gupta |
| **Platform** | Hero Vired |
| **Purpose** | Assignment Submission |

---

## 🎯 Project Objective

- Configure backend (Node.js + Express).
- Setup frontend (React).
- Integrate backend ↔ frontend communication.
- Deploy on **AWS EC2 instance**.
- Implement **scaling using multiple servers + load balancer**.
- Attach **custom domain** using Cloudflare.
- Document full deployment steps + architecture.

---

## 🧰 Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React |
| Backend | Node.js + Express |
| DB | MongoDB Atlas |
| Hosting | AWS EC2 (Ubuntu 22.04) |
| Reverse Proxy | NGINX |
| Process Control | PM2 |
| Security & DNS | Cloudflare |
| Scaling | AWS Load Balancer |

0. Launch EC2 and basic network setup

In AWS Console → EC2 → Launch Instance:

AMI: Ubuntu Server 24.04 LTS

Instance type: t3.micro (for testing) or bigger for production.

Key pair: use your SSH key.

Security Group: allow inbound:

SSH (TCP 22) from your IP

HTTP (TCP 80) from anywhere (0.0.0.0/0)

HTTPS (TCP 443) from anywhere (0.0.0.0/0)

(If using ELB later, ELB will access instance on target port, ensure security group allows ELB)

Launch and note the public IPv4 address.

---

## ⚙️ Backend Setup
SSH command
ssh -i "C:\Users\empir\Downloads\travelmemory-key.pem" ubuntu@13.61.19.179

System dependencies install
sudo apt update && sudo apt upgrade -y
sudo apt install -y git nginx curl

Project clone
git clone https://github.com/UnpredictablePrashant/TravelMemory
cd TravelMemory/backend

npm install
Create .env
Backend directory
MONGO_URI=your_mongo_url
PORT=3000


Start backend:

NGINX reverse proxy.

sudo nano /etc/nginx/sites-available/default

server {
    listen 80;
    server_name travelmemory.dpdns.org;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name travelmemory.dpdns.org;

    ssl_certificate /etc/letsencrypt/live/travelmemory.dpdns.org/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/travelmemory.dpdns.org/privkey.pem;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}



Save & reload:
sudo nginx -t
sudo systemctl restart nginx

sudo npm install -g pm2
pm2 start server.js --name travel-backend
pm2 save
pm2 startup

Backend is now accessible

🎨 Frontend Setup
cd ~/TravelMemory/frontend
npm install
npm run build

sudo nano /etc/nginx/sites-available/default

server {
    listen 80;
    server_name travelmemory.dpdns.org www.travelmemory.dpdns.org;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name travelmemory.dpdns.org www.travelmemory.dpdns.org;

    ssl_certificate /etc/letsencrypt/live/travelmemory.dpdns.org/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/travelmemory.dpdns.org/privkey.pem;

    root /home/ubuntu/TravelMemory/frontend/build;
    index index.html;

    location /api/ {
        proxy_pass http://127.0.0.1:5000/;
    }

    location / {
        try_files $uri /index.html;
    }
}

Update backend URL → src/urls.js:

export const BASE_URL = "https://travelmemory.dpdns.org";


NGINX Restart
sudo nginx -t
sudo systemctl restart nginx

Build frontend:

npm run build
sudo mkdir -p /var/www/travelmemory
sudo cp -r build/* /var/www/travelmemory/

🌐 NGINX Reverse Proxy
sudo nano /etc/nginx/sites-available/travelmemory


server {
    listen 80;
    server_name travelmemory.dpdns.org;

    location /api {
        proxy_pass http://localhost:3000;
    }

    location / {
        root /var/www/travelmemory;
        try_files $uri $uri/ /index.html;
    }
}

sudo ln -s /etc/nginx/sites-available/travelmemory /etc/nginx/sites-enabled/
sudo systemctl restart nginx

🔐 SSL (Certbot)
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d travelmemory.dpdns.org

☁ Cloudflare DNS Setup
Type	Name	Value	Notes
A	@	EC2 Public IP	Required
CNAME	www	Load Balancer URL	Scaling
Proxy Mode	🔶 ON	Required for SSL	
🧪 Testing Routes
URL	Expected Result
/	Frontend loads
/api/hello	Hello World
/api/trip	Data operations
📸 Screenshots Placeholder

📌 Add screenshots here after deployment

Feature	Screenshot
AWS EC2 Console	⬇
NGINX Config	⬇
PM2 Running	⬇
Cloudflare DNS	⬇
App Live View	⬇
🏗 Deployment Architecture
User → Cloudflare → SSL → AWS Load Balancer → EC2 Instances → PM2 → Backend
                                                ↓
                                         NGINX → Frontend

📦 Final Deliverables
Item	Status
Working EC2 Deployment	✔️
React Frontend + Node Backend	✔️
Domain + SSL	✔️
Load Balancing Configured	✔️
Documentation (This File)	✔️
🔗 Repository Link
https://github.com/YOUR-USERNAME/TravelMemory

🚀 Future Improvements

Dockerize frontend & backend

Add CI/CD using GitHub Actions

Implement caching using Redis

Upload media to AWS S3

📌 Credits

Developed & Deployed By:

Priyanshu Gupta | Hero Vrired
Guided by provided deployment objective documentation.
