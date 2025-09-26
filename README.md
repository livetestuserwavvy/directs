# Directus CMS Production Setup on AWS EC2 with Docker, RDS PostgreSQL, and Secure Nginx

## Overview
This guide provides step-by-step instructions to deploy Directus CMS on an AWS EC2 Ubuntu server using Docker, with an external PostgreSQL database hosted on AWS RDS, secured by Nginx with SSL and best security practices.

---

## 1. AWS EC2 Setup
- Launch an Ubuntu 24.04 EC2 instance (t3.medium or larger recommended).
- Assign an Elastic IP for a static public address.
- Attach a security group with:
  - TCP 22 (SSH) from your IP only
  - TCP 80, 443 (HTTP/HTTPS) from 0.0.0.0/0
  - (Optional) TCP 8055 from your IP for Directus admin
- Update and secure the server:
  ```bash
  sudo apt update && sudo apt upgrade -y
  sudo apt install docker.io docker-compose nginx -y
  sudo ufw allow OpenSSH
  sudo ufw allow 80,443/tcp
  sudo ufw enable
  ```
- Harden SSH: disable password login, use key authentication.

---

## 2. AWS RDS PostgreSQL Setup
- Create a PostgreSQL RDS instance (min version 13, production size).
- Set DB username, password, and database name.
- Make the RDS instance **private** (no public access) if possible.
- Add a security group to allow inbound PostgreSQL (TCP 5432) **only** from your EC2's security group.
- Note the RDS endpoint for `.env`.

---

## 3. SSL Certificate
- Use [Let's Encrypt](https://letsencrypt.org/) for free SSL:
  ```bash
  sudo apt install certbot
  sudo certbot certonly --standalone -d your-domain.com
  # Certificates will be in /etc/letsencrypt/live/your-domain.com/
  # Copy them to ./certs/fullchain.pem and ./certs/privkey.pem for Docker
  ```
- Set up auto-renewal with cron.

---

## 4. Project Setup
- Clone this repo to your EC2 instance.
- Copy `.env.example` to `.env` and fill in your secrets and RDS details.
- Place SSL certs in `certs/` directory as `fullchain.pem` and `privkey.pem`.
- Review and edit `nginx.conf` for your domain.

---

## 5. Running Directus & Nginx
- Start services:
  ```bash
  docker-compose --env-file .env up -d
  ```
- Access Directus at `https://your-domain.com`.

---

## 6. Security Best Practices
- Use strong, unique passwords for all services.
- Restrict security groups to only necessary IPs/ports.
- Keep EC2 and Docker images updated.
- Enable automatic security updates on Ubuntu.
- Use HTTPS only; redirect HTTP to HTTPS.
- Set up regular backups for RDS.
- Monitor logs and enable CloudWatch for EC2 and RDS.
- Consider using AWS Secrets Manager for sensitive credentials.

---

## 7. Troubleshooting
- **Directus can't connect to DB:**
  - Check `.env` values and RDS security group.
  - Ensure EC2 can reach RDS endpoint (`telnet rds-endpoint 5432`).
- **SSL issues:**
  - Check cert paths and permissions.
  - Use `docker-compose logs nginx` for errors.
- **Nginx 502/504 errors:**
  - Ensure Directus container is running and healthy.
  - Check proxy settings in `nginx.conf`.
- **Permission errors:**
  - Ensure Docker volumes and certs have correct permissions.

---

## 8. Useful Commands
- Restart services: `docker-compose restart`
- View logs: `docker-compose logs directus` or `docker-compose logs nginx`
- Update Directus: `docker-compose pull && docker-compose up -d`

---

## 9. References
- [Directus Docs](https://docs.directus.io/)
- [AWS RDS Docs](https://docs.aws.amazon.com/rds/)
- [Nginx SSL Guide](https://nginx.org/en/docs/http/configuring_https_servers.html)
- [Docker Compose](https://docs.docker.com/compose/)

---

## 10. File Structure
```
.
├── docker-compose.yml
├── .env.example
├── nginx.conf
├── certs/
│   ├── fullchain.pem
│   └── privkey.pem
└── README.md
```

---

## 11. Security Group Example
- **EC2 Security Group:**
  - Inbound: 22 (your IP), 80/443 (all)
  - Outbound: All
- **RDS Security Group:**
  - Inbound: 5432 (EC2 SG only)
  - Outbound: All

---

## 12. Maintenance
- Regularly update OS, Docker images, and Directus.
- Monitor for vulnerabilities and patch promptly.
- Rotate secrets and credentials periodically.

---

## 13. Support
- For issues, check logs and consult documentation above.
- For AWS-specific help, use AWS Support or forums.

---

**Production-ready and secure by default.**
