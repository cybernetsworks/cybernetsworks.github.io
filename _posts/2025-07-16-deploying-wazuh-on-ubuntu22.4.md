---
layout: post
title:  "Installing Wazuh on Ubuntu24.04"
author: Snowdiamond
categories: [ SOC]
image: /assets/images/wazuh/wazuh.png
tags: [SOC]
---
**INSTALLING WAZUH ON UBUNTU 24.04.2**

**WAZUH**
brief about Wazuh

**VM REQUIREMENTS HERE**
RAM (GB): 4
CPU (cores): 2

**RECOMMENDED**
RAM (GB): 16
CPU (cores): 8

*STORAGE**
According to the Wazuh documentation, an environment with 80 workstations, 10 servers and 10 network devices will require 230GB storage on the indexer for 90days of alerts.

**INSTALLATION PROCESS**
The installation stage consists of three important stages.

Click ![Here]() for instruction on how to install Ubuntu server.

**STEPS** 
- [Wazuh Indexer Installation]()
- [Wazuh Server Installation]()
- [Wazuh Dashboard Installation]()


**WAZUH SERVER INSTALLATION**

Server Node Installation

```
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
```

Wazuh Manager Installation
```
apt-get -y install wazuh-manager
```
Filebeat Installation

```
apt-get -y install filebeat
```

Configure Filebeat

download the preconfigured Filebeat configuration file.

```
curl -so /etc/filebeat/filebeat.yml https://packages.wazuh.com/4.12/tpl/wazuh/filebeat/filebeat.yml
```
Configure the *filebeat.yml*

Edit the filebeat configuration
```
sudo nano /etc/filebeat/filebeat.yml
```
Change the host to the host of the indexer. In this case, my host is *192.168.3.1*

Create a Filebeat keystore for secure authentication credentials

```
sudo filebeat keystore create
```
Add the dafault username and password admin:admin to the secrets keystore.

```
echo admin | filebeat keystore add username --stdin --force
echo admin | filebeat keystore add password --stdin --force
```

Download the alerts template for the Wazuh indexer

```
curl -so /etc/filebeat/wazuh-template.json https://raw.githubusercontent.com/wazuh/wazuh/v4.12.0/extensions/elasticsearch/7.x/wazuh-template.json
chmod go+r /etc/filebeat/wazuh-template.json
```

Wazuh Module for Filebeat Installation 

```
curl -s https://packages.wazuh.com/4.x/filebeat/wazuh-filebeat-0.4.tar.gz | tar -xvz -C /usr/share/filebeat/module
```

Certificate Deployment

```
NODE_NAME=<SERVER_NODE_NAME>
```
Create a directory named *certs* in the *filebeat* directory

```
mkdir /etc/filebeat/certs
```

Compress and move the content of wazuh-certificates into the created directory.

```
tar -xf ./wazuh-certificates.tar -C /etc/filebeat/certs/ ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./admin.pem ./admin-key.pem ./root-ca.pem
```
**Important:** Don't forget to change $NODE_NAME to your node name. In my case it will be 

```
tar -xf ./wazuh-certificates.tar -C /etc/filebeat/certs/ ./node-1.pem ./node-1-key.pem ./admin.pem ./admin-key.pem ./root-ca.pem
```
Rename *$NODE_NAME.pem and $NODE_NAME-key.pem* to *filebeat.pem and filebeat-key.pem*

```
mv -n /etc/wazuh-indexer/certs/$NODE_NAME.pem /etc/filebeat/certs/filebeat.pem
mv -n /etc/wazuh-indexer/certs/$NODE_NAME-key.pem /etc/filebeat/certs/filebeat-key.pem
```

Change permission and ownership

```
chmod 500 /etc/filebeat/certs
chmod 400 /etc/filebeat/certs/*
chown -R root:root /etc/filebeat/certs
```

Configure the Wazuh Indexer Connection

```
echo '<INDEXER_USERNAME>' | /var/ossec/bin/wazuh-keystore -f indexer -k username
echo '<INDEXER_PASSWORD>' | /var/ossec/bin/wazuh-keystore -f indexer -k password
```
**NOTE:** We are using the default credentials
So, the command will now be

```
echo 'admin' | /var/ossec/bin/wazuh-keystore -f indexer -k username
echo 'admin' | /var/ossec/bin/wazuh-keystore -f indexer -k password
```

Download MIP Ainimal CentOs 7.9  [HERE](https://vault.centos.org/7.9.2009/isos/x86_64/).


**STEPS**

VM Setup




!["CentOS"](/assets/images/centos/centos-3.png)



ENJOY !!!