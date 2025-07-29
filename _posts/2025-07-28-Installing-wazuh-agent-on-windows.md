---
layout: post
title:  "Installing Wazuh Agent on Windows"
author: Snowdiamond
categories: [ SOC]
image: /assets/images/wazuh/wazuh.png
tags: [SOC]
---
**INSTALLING WAZUH AGENT ON WINDOWS**

**INSTALLATION OPTION**
There are two installation options available for windows users:
- GUI (Graphical User Interface)
- CLI (Command Line Interface)


**GUI**
Using this method requires three steps

**STEP ONE:** Installer Download

- Download the [Windows Installer](https://packages.wazuh.com/4.x/windows/wazuh-agent-4.12.0-1.msi) 

**STEP TWO:** Installation

- Launch the installer.

- Accept the license and agreement then click *Install*
!["wazuh"](/assets/images/wazuh/windows-agent-installer.png)

- When the windows to complete the setup pops up, tick the *Run Agent Configuration Interface* box then click *Finish*
!["Wazuh"](/assets/images/wazuh/windows-agent-installer-config-select.png)

- Click *Yes* When prompted *If you want to allow the application to make a difference.

**STEP THREE:** Configuration
- Enter the wazuh manager ip in the provided space to allow agent's communication with the wazuh manager and click *Save*.
!["Wazuh"](/assets/images/wazuh/wazuh-agent-manager-ip-config.png)

- Start the agent. Click *Manage* then click *Start* to start the agent
!["Wazuh"](/assets/images/wazuh/wazuh-agent-start.png)

!["Wazuh"](/assets/images/wazuh/agent-start-confirmation-windows.png)

**CLI**

ENJOY !!!