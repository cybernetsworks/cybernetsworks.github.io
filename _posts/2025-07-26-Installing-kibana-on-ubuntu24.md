---
layout: post
title:  "Installing Kibana on Ubuntu 24.04"
author: Snowdiamond
categories: [ SOC]
image: /assets/images/elk/kibana.png
tags: [SOC]
---
**INSTALLING KIBANA9.x ON 24.04**

**IMPORTANT** 

**KIBANA**

**NOTE:** This is a single node installation.

**INSTALLATION PROCESS**
Elasticsearch requires java to runs perfectly. This installation process comes with a bundled version of OpenJDK.

**STEP ONE:** Kibana PGP Key Importation

```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
```

**STEP TWO:** Kibana Installation

- Download the Kibana package
```
wget https://artifacts.elastic.co/downloads/kibana/kibana-9.0.3-amd64.deb
```

- Compare the hash of the downloaded package and the published checksum
```
shasum -a 512 kibana-9.0.3-amd64.deb
```

- Package Installation
```
sudo dpkg -i kibana-9.0.3-amd64.deb
```

**STEP THREE:** Configure

- Disable the security 
```
xpack.security.enabled:flase
```

The security is disabled for this testing but will be enabled in a real scenario.
Click ![Here](https://cybernetsworks.github.io/setting-up-an-ubuntu-server-vm/) for instruction on how to install Ubuntu server.