---
layout: post
title:  "Installing OpenCTI on Windows Ubuntu24.04"
author: Snowdiamond
categories: [ SOC]
image: /assets/images/opencti/opencti.jpg
tags: [SOC]
---
**INSTALLING OPENCTI ON UBUNTU24**

**REQUIREMENT**

Minimum Requirement (For Testing)
**Note:** This is more suitable for

```
- Testing and very small datasets
- Limited connector usage
- Elasticsearch/OpenSearch running with small heap

This might have the consequences of UI appearing slow.
```

```
- OS: Ubuntu Server 22.04 LTS
- CPU: 2 cores
- RAM: 8 GB
- Memory: 40GB SSD
- Container runtime: Docker + Docker Compose

```

Recommended Minimum (For Efficient Operation)
**Note:** This is more suitable for 

```
- Smooth UI performance
- Stable search indexing
- Multiple connectors
- Realistic SOC-Lab behavior
```

```
- OS: Ubuntu Server 22.04 LTS
- CPU: 4 cores
- RAM: 12-16 GB
- Memory: 60-100GB SSD
- Container runtime: Docker + Docker Compose

```

**INSTALLATION PROCESS**

**NOTE:** This installation process assumes you already have an ubuntu server installed and running. if not see Here on how to install an Ubuntu server.

- System update
- Install docker and docker compose if not already present
- 
- Install OpenCTI


**STEP ONE:** System Update
```
sudo apt update
```
**STEP TWO:** Docker Compose Installation (If not already present)

- Certificate Installation

```
sudo apt update
sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo $VERSION_CODENAME) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
- system update
```
sudo apt update
```
- Docker compose installation
```
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
- Installation Verification
```
docker --version
docker compose version.
```
- Enable Docker for Non-Root User
```
sudo usermod -aG docker $USER
sudo reboot
```
**STEP THREE:** Creat OpenCTI directory 
```
mkdir opencti
```

**STEP FOUR:** Navigate to the director and clone opencti repository.

```
cd opencti
git clone https://github.com/OpenCTI-Platform/docker.git
```

**STEP FOUR:** Environment Configuration

- Copy the sample environment for editing
```
cp .env.samp .env
```

- Edit the new `.env`
```
sudo nano .env
```
- Edit the following configuration
```
MINIO_ROOT_PASSWORD
RABBITMQ_DEFAULT_PASS
OPENCTI_ADMIN_PASSWORD
OPENCTI_ADMIN_TOKEN (Generate the token using online uuid generator [Online uuid](https://www.uuidgenerator.net/version4))
OPENCTI_HEALTHCHECK_ACCESS_KEY (Use one of the connector id provided)

```

**STEP FIVE:** Run the container
```
docker compose up -d
```

**STEP SIX:** Accessing the Platform
```
On your Ubuntu Desktop, access the OpenCTI dashboard using http://openctiip:8080
```
**Adding Connector (AlienVault)**

```
- Navigate to the [Opencti github repository]()
- Navigate to connector and select connector of your choice.
- open the docker-compose.yml file of the connector.
- Copy the code and add to your opencti docker-compose file. Add it just before the volume line --image here-- 
- To prevent the container from running on its own, ensure it depends on the OpenCTI. ---image here---
- Add your OPENCTI TOKEN
- Generate a connector id using the [online uuid generator]() and add as a connector id
- Change the ALIENVAULT_PULSE_START_TIMESTAMP to prevent downloading too much data
- Add your alienvault key (You can get this from your alienvault account)
- RUN `docker compose up -d` to build the image
```


