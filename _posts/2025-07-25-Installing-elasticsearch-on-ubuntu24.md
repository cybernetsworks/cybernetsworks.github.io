---
layout: post
title:  "Installing ELK Stack on Ubuntu 24.04"
author: Snowdiamond
categories: [ SOC]
image: /assets/images/elk/elk.png
tags: [SOC]
---
**INSTALLING ELASTICSEARCH 9.x ON 24.04**

**IMPORTANT** Elasticsearch 8.x upwards has security (TLS + authentication) automatically enabled
**ELASTICSEARCH**

**NOTE:** This is a single node installation.

**INSTALLATION PROCESS**
Elasticsearch requires java to runs perfectly. This installation process comes with a bundled version of OpenJDK.

**STEP ONE:** Elasticsearch PGP Key Importation

```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
```

**STEP TWO:** Elasticsearch Installation

- Download the elasticsearch package
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.0.3-amd64.deb
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.0.3-amd64.deb.sha512
```

- Compare the hash of the downloaded package and the published checksum
```
shasum -a 512 -c elasticsearch-9.0.3-amd64.deb.sha512
```

- Package Installation
```
sudo dpkg -i elasticsearch-9.0.3-amd64.deb
```

**STEP THREE:** Configure

```
sudo nano /etc/elasticsearch/elasticsearch.yml
```

- Disable the security 
```
xpack.security.enabled:flase
```

The security is disabled for this testing but will be enabled in a real scenario.
Click ![Here](https://cybernetsworks.github.io/setting-up-an-ubuntu-server-vm/) for instruction on how to install Ubuntu server.