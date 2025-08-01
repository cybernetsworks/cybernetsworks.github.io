---
layout: post
title:  "Installing Wazuh Agent on Linux"
author: Snowdiamond
categories: [ SOC]
image: /assets/images/wazuh/wazuh.png
tags: [SOC]
---
**INSTALLING WAZUH AGENT ON LINUX**
**IMPORTANT:** This installation process requires root privilege so you need to remain root user during the installation.

Switching to root user 

```
sudo su
```

Enter the root password and press *Enter*

**INSTALLATION PROCESS**

**STEP ONE:** Repository Addition 

- Install the GPG key

```
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
```

- Add the Repository

```
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
```

- Run an update

```
apt-get update
```
**STEP TWO:** Wazuh Agent Deployment

- Deploy the agent by running the command below
```
WAZUH_MANAGER="IP ADDRESS" apt-get install wazuh-agent
```
**NOTE:** Insert the wazuh manager ip address where "IP ADDRESS" is written.

- Enable and start the Wazuh agent service
```
systemctl daemon-reload
systemctl enable wazuh-agent
systemctl start wazuh-agent
```

- Disable Wazuh updates
```
sed -i "s/^deb/#deb/" /etc/apt/sources.list.d/wazuh.list
apt-get update
```
ENJOY !!!