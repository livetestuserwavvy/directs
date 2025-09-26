# How to Connect Your GoDaddy Domain to AWS EC2 (Directus)

Follow these steps to point your GoDaddy domain to your AWS EC2 instance for Directus CMS:

## 1. Get Your EC2 Public IP
- Go to AWS EC2 Console
- Find your instance and copy its public IPv4 address

## 2. Update GoDaddy DNS
- Log in to GoDaddy
- Go to "My Products" > "DNS" for your domain
- Add or edit an "A" record:
  - Host: @
  - Points to: <your EC2 public IPv4 address>
  - TTL: 600 seconds (or default)
- Save changes

## 3. Wait for DNS Propagation
- DNS changes may take up to 30 minutes to propagate

## 4. Update Your .env File
- Set `DOMAIN=your-domain.com` in your `.env` file

## 5. Security Group Settings
- In AWS EC2, make sure your security group allows:
  - TCP 80 (HTTP)
  - TCP 443 (HTTPS)
  - TCP 8055 (Directus)
  - SSH (22) only from trusted IPs

## 6. Test Your Setup
- Visit `https://your-domain.com` in your browser
- You should see Directus running (after Nginx/SSL setup)

---
For more help, see AWS and GoDaddy documentation or ask your hosting provider.
