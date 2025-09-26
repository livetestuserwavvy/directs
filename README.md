# Directus CMS on AWS EC2 (Ubuntu) with Docker, PostgreSQL, Nginx & SSL

## Overview
This guide helps you deploy Directus CMS securely on an AWS EC2 Ubuntu server using Docker, PostgreSQL, Nginx (with SSL), and best security practices.

## Prerequisites
- AWS EC2 Ubuntu 22.04/24.04 instance
- Domain name pointed to your EC2 public IP
- Security Group: Allow TCP 80, 443, 8055 (Directus), SSH (22) only from trusted IPs

## Steps

### 1. Update & Install Docker, Docker Compose, Nginx
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install docker.io docker-compose nginx certbot python3-certbot-nginx -y
```

### 2. Clone your repo & set up environment
```bash
git clone <your-repo-url>
cd directs
cp .env.example .env
# Edit .env with strong secrets and your domain
```

### 3. Start Directus & PostgreSQL
```bash
sudo docker-compose up -d
```

### 4. Configure Nginx (SSL)
- Edit `nginx.conf` with your domain
- Copy to `/etc/nginx/sites-available/directus`
- Enable site:
```bash
sudo ln -s /etc/nginx/sites-available/directus /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```
- Get SSL certificate:
```bash
sudo certbot --nginx -d your-domain.com
```

### 5. Security Best Practices
- Use strong, random secrets in `.env`
- Set up UFW:
```bash
sudo ufw allow 'Nginx Full'
sudo ufw allow 8055/tcp
sudo ufw enable
```
- Restrict SSH to your IP only
- Keep system and Docker images updated
- Use AWS IAM roles and least privilege
- Enable automatic security updates:
```bash
sudo apt install unattended-upgrades
```

### 6. Access Directus
- Go to `https://your-domain.com` (Nginx reverse proxy to port 8055)

## Example .env
See `.env.example` for secure configuration.

## Notes
- Never commit real secrets to git
- For production, use secure passwords and rotate regularly
- Monitor logs and enable alerts for suspicious activity

---
For more info: [Directus Docs](https://docs.directus.io) | [PostgreSQL Security](https://www.postgresql.org/docs/current/security.html) | [Nginx SSL](https://nginx.org/en/docs/http/configuring_https_servers.html)