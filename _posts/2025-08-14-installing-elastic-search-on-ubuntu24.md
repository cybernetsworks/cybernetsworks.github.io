---
layout: post
title:  "Installing Elasticsearch on Ubuntu24.04"
author: Snowdiamond
categories: [ SOC]
image: /assets/images/opencti/opencti.jpg
tags: [SOC]
---
**INTSTALLING ELASTICSEARCH ON UBUNTU24**

**ELASTICSEARCH**

**INSTALLATION PROCESS**

- Run system update
- Download the public signing key
- Install the Apt package
- Dowload the elasticsearch package
- Install elasticsearch
- Enable and start elasticsearch

**STEP ONE:** Run an update

```
sudo apt update
```

**STEP TWO:** Download and install public signing key

```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
```

**STEP THREE:** Install the Apt package.

```
sudo apt-get install apt-transport-https
```

**STEP FOUR:** Download the elasticsearch package
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.19.2-amd64.deb
```

- Download the hash file (a sha512 checksum)
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.19.2-amd64.deb.sha512
```
- Verify Integrity
```
shasum -a 512 -c elasticsearch-8.19.2-amd64.deb.sha512
```
**STEP FIVE:** Install the package
```
sudo dpkg -i elasticsearch-8.19.2-amd64.deb
```

**STEP SIX:** Enable and start the service
```
sudo systemctl enable elsaticsearch
sudo systemctl start elasticsearch
``` 

