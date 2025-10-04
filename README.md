# Exposing Local Apache Website to the Internet using FRP  

## Introduction
- **FRP (Fast Reverse Proxy)** is a high-performance reverse proxy that allows you to expose local servers behind NAT/firewall to the public internet.  
- We host a **website using Apache locally** and expose it to the internet via a **VPS running FRP server**.  

---

### 1. Launch EC2 Instance  
- Go to AWS Console, then EC2, then Launch Instance  
- Choose **Ubuntu**  
- Create and Use key pair
- Use Putty
- Allow **HTTP (80)**, **HTTPS (443)**, and **SSH (22)**  
- Launch instance
  
## Launched EC2 Instance
![EC2-Launched-Instance](/frpimages/ec2.png)

---

## 2. Download FRP Server on created AWS Ubuntu server and on your local machine both ( I am using kali linux as local )

```bash
# Download FRP
wget https://github.com/fatedier/frp/releases/download/v0.61.0/frp_0.61.0_linux_amd64.tar.gz
tar -xvzf frp_0.61.0_linux_amd64.tar.gz
cd frp_0.61.0_linux_amd64
```

# 3. Create FRP server config on Ubuntu

```bash
nano frps.ini
```
- Copy and paste below commands.
  
```bash
[common]
bind_port = 7000
```
- Start FRP server

```bash
./frps -c frps.ini
```

# 4. Create a website on local machine

```bash
# Install Apache
sudo apt update && sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2

# Add sample website
cd /var/www/html/
sudo nano index.html

#Paste your own customised web code for example
<html>
HI FROM FRP CLIENT FROM KALI
</html>

#verify locally
http://localhost
```
![Apache-Website-Localhost](/frpimages/apacheweblocal.png)

# 5. Setup FRP Client on Local PC
```bash
# Go to FRP directory
cd frp_0.61.0_linux_amd64

# Create client config
nano frpc.ini

# Copy and paste this
[common]
server_addr = 51.20.136.11 #write your own public vps ip
server_port = 7000

[web]
type = tcp
local_port = 80
remote_port = 8081

# Start FRP Client
./frpc -c frpc.ini
```
**Note** - Please allow port 7000 and 8081 from security groups on your AWS Ubuntu Server as shown below.

![AWS-Ports](/frpimages/securitygroups.png)

# FRP Client connected to server

![FRP-Success](/frpimages/frpconnected.png)

# 6. Test

```
http://51.20.136.11:8081 #Edit with your public ip of AWS Ubuntu Server
```
- It will show your apache website that hosted locally and it is now publicly accesible using FRP

# Website Accesible

![Success](/frpimages/webaccessible.png)
