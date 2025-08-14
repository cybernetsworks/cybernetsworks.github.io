---
layout: post
title:  "Integrating Suricata with Wazuh"
author: Snowdiamond
categories: [ SOC]
image: /assets/images/suricata/suricata.jpg
tags: [SOC]
---
**INTEGRATING SURICATA WITH WAZUH**

**REASONS**

**INSTALLATION PROCESS**

**STEP ONE:** Run an update

```
sudo apt update
```

**STEP TWO:** Suricata Installation

["Installing Suricata"](https://cybernetsworks.github.io/Installing-suricata-on-ubuntu24/)

**STEP THREE:** Wazuh Client Installation

["Installing Wazuh Client on Linux"](https://cybernetsworks.github.io/Installing-wazuh-agent-on-linux/)
 
**STEP FOUR:** Suricata Logging Configuration

**STEP FIVE:** Wazuh Agent Configuration for Log Collection

- Enter the below command to access the wazuh agent configuration file
```
sudo nano /var/ossec/etc/ossec.conf
```

- Add the below configuration to the existing configuration

```
  <localfile>
    <log_format>json</log_format>
    <location>/var/log/suricata/eve.json</location>
  </localfile>
```
- Save the configuration 
```
press ctrl x
press y
press enter
```

**STEP SIX:** Check for Configuration Syntax Erorr
```
sudo /var/ossec/bin/wazuh-syscheckd -t
```
*There should be no response.*

**STEP SEVEN:** Restart wazuh client

```
sudo systemctl restart wazuh-agent
```

**STEP EIGHT:** Confirm 
The security is disabled for this testing but will be enabled in a real scenario.