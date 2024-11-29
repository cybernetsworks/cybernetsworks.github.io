---
layout: post
title:  "Setting Up Internal Network on Ubuntu Desktop"
author: jane
categories: [ Jekyll, tutorial ]
image: assets/images/UbuntuDesktop-Interface.png
tags: [Networking]
---
**CONFIGURING YOUR UBUNTU DESKTOP VM WITH A STATIC IP ADDRESS**

**Step 1** Set your network adapter to use *Internal Network*
- Click on the **Ubuntu  Desktop VM** and click on settings
- Select Network to display the network adapters.
- Change the settings as shown in the below screenshot.
- Click *Okay* and start the machine.
![Ubuntu Desktop](/assets/images/UbuntuDesktopNetwork1.png)
  
**Step 2** Assign a private IP address and DNS server to your Ubuntu desktop.
 - Click on the network icon
![Ubuntu Desktop](/assets/images/UbuntuDesktopNetwork2.png)
- Click *Wired Connected* and select *Wired Connection* from the dropdown list.
![Ubuntu Deaktop](/assets/images/UbuntuDesktopNetwork3.png)
- Click the settings icon 
![Ubuntu Desktop](/assets/images/UbuntuDesktopNetwork4.png)
- Click on IPv4
- check *Manual*
- Enter your private id address, netmask and gateway address.

**NOTE** The gateway address must be same as the address of the gateway interface you aim to connect with. <br>
  Example: if gateway interface has: 192.168.59.1 as address, you must use same address in this Ubuntu Desktop configuration.  
![Ubuntu Deaktop](/assets/images/UbuntuDesktopNetwork5.png)
