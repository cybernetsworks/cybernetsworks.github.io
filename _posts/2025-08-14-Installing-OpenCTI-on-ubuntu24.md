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
- Memory: 50GB
- Network: Static IP recommended for integrations

**INSTALLATION PROCESS**
- System update
- Dependences Installation
- Application files download
- Install the Main Platform
- Install the Python Modules
- Start the Application

**STEP ONE:** Run an update

```
sudo apt update
```

**STEP TWO:** Dependences Installation
The following dependences are neccessary for OpenCTI to work:
- nodejs
- python3
- Elasticsearch
- Redis
- RabbitMQ
- MinIO

```
sudo apt install -y wget curl gnupg2 software-properties-common apt-transport-https ca-certificates lsb-release git build-essential
```

Node.js Installation
```
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install nodejs -y
```


Python3 Installation


Ubuntu 24.04 comes with python 3.12.

Verify installation
```
python3 --version
```

Run a System-wise Python Installation
```
sudo apt install -y python3-dev python3-setuptools python3-wheel
sudo apt install -y build-essential

```

[Installing Elasticsearch](https://cybernetsworks.github.io/installing-elastic-search-on-ubuntu24/)

- Elasticsearch configuration
```
sudo nano /etc/elasticsearch/elasticsearch.yml
```

Enter the following configuration and restart elasticsearch
```
cluster.name: opencti
node.name: node-1
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
network.host: 127.0.0.1
http.port: 9200
xpack.security.enabled: false
xpack.security.enrollment.enabled: false
```

Restart the service
```
sudo systemctl restart elasticsearch
```

[Installing Redis](https://cybernetworks.github.io/installing-redis-on-ubuntu/)

- Redis Configuration
```
sudo nano /etc/redis/redis.conf
```

Add the vm ip address to bind. It should look like the below
!["redis"](/assets/images/opencti/redis-bind-ip.png)

change protected mode to no
!["redis"](/assets/images/opencti/protected-mode.png)

Restart the service 
```
sudo systemctl restart redis-server
```
- Test redis connectivity

Test 1 
```
redis-cli ping
```
This should return **pong**

Test 2
```
redis-cli -h ip address binded (vm ip address) to ping
```
This should return **pong**

[Install RabitMQ](https://cybernetsworks.github.io/installing-rabbitmq-on-ubuntu24/)

- Create OpenCTI user
```
sudo rabbitmqctl add_user opencti changeme
sudo rabbitmqctl set_user_tags opencti administrator
sudo rabbitmqctl set_permissions -p / opencti ".*" ".*" ".*"
``` 

Restart Rabbitmq service
```
sudo systemctl restart rabbitmq-server
```

[Install MinIO](https://cybernetsworks.github.io/installing-minio-on-ubuntu/)

- Create Configuration
```
sudo nano /etc/default/minio
```

Add and Save the Below Content
```
MINIO_ROOT_USER=opencti
MINIO_ROOT_PASSWORD=changeme123
MINIO_VOLUMES="/opt/minio/data"
MINIO_OPTS="--console-address :9001"
```

- Enable and Start MiniIO
```
sudo systemctl enable minio
sudo systemctl start minio
```


**OPENCTI INSTALLATION**

- Enable corepack
```
sudo corepack enable
```

- File Download
```
https://github.com/OpenCTI-Platform/opencti/releases/download/6.7.11/opencti-release-6.7.11.tar.gz
```
- Extract the file 
```
tar -xvzf opencti-release-6.7.11.tar.gz
```

- Move the files to installation directory.
``` 
sudo mkdir -p /opt/opencti
sudo mv opencti/* /opt/opencti/
```

- Login as root to make installation easier. Ensure you are in the opencti directory
```
sudo su
cd /opt/opencti
```

- Install yarn
```
corepack yarn install
```

- Temporarily Bypass externally-managed environment:
```
- log out of root

exit

- find and temporarily move the EXTERNALLY-MANAGED file

sudo find /usr -name "EXTERNALLY-MANAGED" -type f

- move it temporarily. if the path of the file is different, edit it accordingly 

sudo mv /usr/lib/python3.12/EXTERNALLY-MANAGED /usr/lib/python3.12/EXTERNALLY-MANAGED.bak
```

- Build Frontend
```
sudo su - opencti
cd /opt/opencti
corepack yarn build
```
**NOTE:** Some typescript warnings/errors might pop up. They are non-critical issues and won't affect functionality.

- Create production configuration
```
cp config/default.json config/production.json
nano config/production.json
```
- Edit the configuration 
```
nano config/production.json

- Add the following configuration
base_url: "http://vm ip address:4000"
```

Before initializing OpenCTI Database, check the version of yarn and the specified version in the package manager and change it accordingly.

Enter this command to check the version specified in the packageManager
```
grep -n "packageManager" package.json
```
!["package manager"](/assets/images/opencti/protected-mode.png)
In my case 4.9.2 was specified in the packageManager

Enter this command to check the highest version of yarn available
```
npm view yarn versions --json | grep "4\."
```
!["yarn-version"](/assets/images/opencti/yarn-version.png)

Change the packageManager accordingly
```
sed -i 's/"packageManager": "yarn4.9.2"/"packageManager": "yarn@2.4.3"/g' package.json
sudo rm /usr/bin/yarn
sudo rm -f /usr/local/bin/yarn
sudo rm -f /usr/bin/yarnpkg
npm cache clean --force
npm install -g yarn@2.4.3
hash -d yarn
```

Confirm the version
```
yarn --version
```

- Initialize OpenCTI Database
```
export NODE_ENV=production
npm run schema
```


