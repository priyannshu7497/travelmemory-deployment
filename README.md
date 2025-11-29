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

# 🌍 Travel Memory App Deployment

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

---

## 🚀 Deployment Steps

### 0️⃣ Launch AWS EC2 Instance

- **AMI:** Ubuntu Server 24.04 LTS  
- **Instance Type:** t3.micro  
- **Security Rules Allowed:**  
  - SSH → Port 22  
  - HTTP → Port 80  
  - HTTPS → Port 443  

---

### 1️⃣ SSH to Instance

```bash
ssh -i "C:\Users\empir\Downloads\travelmemory-key.pem" ubuntu@13.61.19.179

2️⃣ Install Dependencies
sudo apt update && sudo apt upgrade -y
sudo apt install -y git nginx curl

3️⃣ Clone Repository
git clone https://github.com/UnpredictablePrashant/TravelMemory
cd TravelMemory/backend
npm install

4️⃣ Configure Backend .env
MONGO_URI=your_mongo_url
PORT=3000

5️⃣ Start Backend Using PM2
sudo npm install -g pm2
pm2 start server.js --name travel-backend
pm2 save
pm2 startup

6️⃣ NGINX Backend Reverse Proxy
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
7️⃣ Frontend Build
cd ~/TravelMemory/frontend
npm install
npm run build

Update:
export const BASE_URL = "https://travelmemory.dpdns.org";
Copy build:
sudo mkdir -p /var/www/travelmemory
sudo cp -r build/* /var/www/travelmemory/

8️⃣ Final NGINX Frontend Config
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

    root /var/www/travelmemory;
    index index.html;

    location /api/ {
        proxy_pass http://127.0.0.1:3000/;
    }

    location / {
        try_files $uri /index.html;
    }
}

Restart:
sudo nginx -t
sudo systemctl restart nginx

9️⃣ SSL Certificate via Certbot
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d travelmemory.dpdns.org

☁ Cloudflare DNS Configuration
Type	Name	Value	Status
A	@	EC2 Public IP	🔶 Proxied
CNAME	www	domain	🔶 Proxied

🧪 Testing
URL	Result
/	Frontend works
/api/hello	Backend API success
/api/trip	CRUD works

🏗 Architecture

User → Cloudflare → SSL → AWS Load Balancer → EC2 Instances → PM2 → Backend
                                               ↓
                                           NGINX → Frontend
📦 Final Deliverables
Item	Status
Working EC2 Deployment	✔️
Frontend + Backend	✔️
Domain + SSL	✔️
Load Balancer Ready	✔️
Complete Documentation	✔️

🔗 Repository Link

👉 https://github.com/UnpredictablePrashant/TravelMemory

🚀 Future Improvements

Dockerize services

Add CI/CD using GitHub Actions

Redis Caching

File storage with AWS S3

🏁 Credits

Developed By Priyanshu Gupta — Hero Vired
