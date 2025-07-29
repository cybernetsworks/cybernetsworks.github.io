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
This is an open-source SIEM tool that provides security log analysis, vulnerability detection, security configuration assessment, and regulatory compliance.

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

Click ![Here](https://cybernetsworks.github.io/setting-up-an-ubuntu-server-vm/) for instruction on how to install Ubuntu server.

**STEPS** 
- [Wazuh Indexer Installation](https://cybernetsworks.github.io/installing-wazuh-indexer/)
- [Wazuh Server Installation](https://cybernetsworks.github.io/installing-wazuh-server/)
- [Wazuh Dashboard Installation](https://cybernetsworks.github.io/installing-wazuh-dashboard/)

Access your dashboard by visiting the ip address used for the setup process. 
for example http://192.168.3.1 this is because i used 192.168.3.1 for the setup.

After a successful installation, the next step would be to monitor an endpoint. This requires 

- [Setting up wazuh agents on endpoints](https://cybernetsworks.github.io/installing-wazuh-agent-on-endpoints/)

ENJOY !!!