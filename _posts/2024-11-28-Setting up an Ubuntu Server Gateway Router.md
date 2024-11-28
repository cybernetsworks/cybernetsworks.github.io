---
layout: post
title:  "Three ways communication server setup (Ubuntu Server)"
author:  Snowdiamond
categories: [ Jekyll, post ]
image: assets/images/14.jpg
tags: Networking
---
### Setting Up an Ubuntu Server with Three Network Interfaces
**AIM OF CONFIGURATION**
To have the Ubuntu server act as a gateway that allows internal communication on two different networks and external/internet communication via another interface using *NAT*

**STEPS**
- Download and Install an Ubuntu Server
- Configure the Ubuntu Server

**CONFIGURING YOUR UBUNTU SERVER (GATEWAY)** <br>
**NOTE:** We need our gateway server to have three interfaces to be able to achieve our goal.
**Step 1** Enable three network adapters on your Ubuntu server (Gateway)
 - configure two of the adapter to use *Internal Network* and the last one to use *NAT*
   
   **REASON:** The *internal networks* allows private/internal access/communication while the *Nat* allows external (Internet) access/communication.
   
   - Click on the Ubunutu Server vm
   - Click on settings
   - Click on Network to access and configure the network adapter as shown below.
     ![Ubuntu Desktop](/assets/images/gateway_adapter_1.png)
     ![Ubuntu Desktop](/assets/images/gateway_adapter_2.png)
     
     **IMPORTANT:** Give each of the internal networks a different name to avoid confusion during setup. *inet and inet1* were used in the above example.
     ![Ubuntu Desktop](/assets/images/gateway_adapter_3.png)
   - Start the Ubuntu Server VM and login.
**Step 2** Confirm the name of the network adapters.
- type the command *ip a* to display network adapter names.
    
**Step 3** Assign a private IP address and DNS server to your Ubuntu Server (Gateway).
  Enter the below command to edit your Ubuntu Server network configuration.
  sudo nano /etc/netplan/00-installer-config.yaml
  **NOTE:** You can use any name. it doesn't have to be *"00-installer-config"*
  ![Ubuntu Desktop](/assets/images/gateway-configuration.png)
