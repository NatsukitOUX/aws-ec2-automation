# aws-ec2-automation

# aws-ec2-automation

## 📌 Overview
This project automates the provisioning of an AWS EC2 instance with:
- Apache web server installation and startup
- Swap file creation (1GB) with persistent configuration
- EC2 instance metadata retrieval (instance ID displayed on web page)
- Tailscale VPN integration using pre-authentication keys

## 🚀 Technologies
- AWS EC2
- Bash scripting (User-data automation)
- Apache2 (Web server)
- Linux (Ubuntu)
- Tailscale VPN

## 🛠️ Features
- Fully automated EC2 instance setup using user-data script
- Instance ID dynamically displayed on `index.html`
- Swap space automatically configured and persistent across reboots
- Secure networking via Tailscale VPN

## 📷 Screenshots


## 📜 Script
```bash
#!/bin/bash
apt update && apt upgrade -y
apt install apache2 lynx curl -y
systemctl enable --now apache2

# Swap setup
if [ ! -f /swapfile ]; then
  dd if=/dev/zero of=/swapfile bs=1M count=1024
  chmod 600 /swapfile
  mkswap /swapfile
  swapon /swapfile
  echo "/swapfile swap swap defaults 0 0" >> /etc/fstab
fi

# EC2 Metadata
TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" \
 -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
EC2ID=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" \
 http://169.254.169.254/latest/meta-data/instance-id)

echo "<center><h1>This is AWS EC2 Instance: $EC2ID</h1></center>" \
 > /var/www/html/index.html

# Tailscale
curl -fsSL https://tailscale.com/install.sh | sh
# Run with:
 tailscale up --authkey <your-key> --hostname "MyEC2"