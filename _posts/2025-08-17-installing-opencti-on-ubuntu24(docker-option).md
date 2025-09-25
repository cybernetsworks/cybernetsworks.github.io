---
layout: post
title:  "Installing OpenCTI on Ubuntu24.04"
author: Snowdiamond
categories: [ SOC]
image: /assets/images/opencti/opencti.jpg
tags: [SOC]
---
**INSTALLING OPENCTI ON UBUNTU24**

**REQUIREMENT**

Minimum Requirement
- CPU: 4 cores
- RAM: 8GB
- Memory: 100GB
- Network: Static IP recommended for integrations

**INSTALLATION PROCESS**
- System update
- Install docker and docker compose if not already present
- Configure system limit
- Install OpenCTI


**STEP ONE:** Run an update

```
sudo apt update
```

**STEP TWO:** Install docker and docker compose
- Docker Installation
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
```
- Docker Compose Installation
```
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

**STEP THREE:** Configure System Limits
```
echo "vm.max_map_count=1048575" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

**STEP FOUR:** Install OpenCTI
- Create Directory Structure
```
mkdir -p /opt/opencti
cd /opt/opencti
```
- Download OpenCTI Docker Compose
```
wget https://raw.githubusercontent.com/OpenCTI-Platform/docker/master/docker-compose.yml
```
- Create Environment File
```
cat > .env << EOF
OPENCTI_ADMIN_EMAIL=admin@opencti.local
OPENCTI_ADMIN_PASSWORD=admin
OPENCTI_ADMIN_TOKEN=$(cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 64 | head -n 1)
OPENCTI_BASE_URL=http://vm ip:8080
MINIO_ROOT_USER=opencti
MINIO_ROOT_PASSWORD=$(cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 32 | head -n 1)
RABBITMQ_DEFAULT_USER=opencti
RABBITMQ_DEFAULT_PASS=$(cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 32 | head -n 1)
SMTP_HOSTNAME=localhost
ELASTIC_MEMORY_SIZE=4G
EOF
```
