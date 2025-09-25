---
layout: post
title:  "Installing minIO on Ubuntu24.04"
author: Snowdiamond
categories: [ SOC]
image: /assets/images/opencti/opencti.jpg
tags: [SOC]
---
**INSTALLING MINIO ON UBUNTU24**

**INSTALLATION PROCESS**
- Update system
- Create MinIO user
- Create MinIO directories
- Download MinIO
- Create MinIO service file

**STEP ONE:** Run an update

```
sudo apt update
```

**STEP TWO:** Create MinIO User

```
sudo useradd -r minio-user -s /sbin/nologin
```

**STEP THREE:** Create MinIO Directories.

```
sudo mkdir -p /opt/minio/bin
sudo mkdir -p /opt/minio/data
```

**STEP FOUR:** Download MinIO
```
wget https://dl.min.io/server/minio/release/linux-amd64/minio
sudo mv minio /opt/minio/bin/
sudo chmod +x /opt/minio/bin/minio
sudo chown -R minio-user:minio-user /opt/minio/
```

**STEP FIVE:** Create Minio Service File
```
sudo nano /etc/systemd/system/minio.service
```
- Enter and save the below configuration
```
[Unit]
Description=MinIO
Documentation=https://docs.min.io
Wants=network-online.target
After=network-online.target
AssertFileIsExecutable=/opt/minio/bin/minio

[Service]
WorkingDirectory=/opt/minio/
User=minio-user
Group=minio-user
Environment=MINIO_ROOT_USER=opencti
Environment=MINIO_ROOT_PASSWORD=changeme123
ExecStart=/opt/minio/bin/minio server --address ip address:9000 --console-address ip address:9001 /opt/minio/data
Restart=always
LimitNOFILE=65536
TasksMax=infinity
TimeoutStopSec=infinity
SendSIGKILL=no

[Install]
WantedBy=multi-user.target
```
