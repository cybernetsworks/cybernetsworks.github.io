---
layout: post
title: "CREATING A SANDBOX NETWORK"
author: Snowdiamond
categories: [Jekyll, post]
tags: [vm]
image: assets/images/UbuntuDesktop-Interface.png
---
**WHAT IS A SANDBOX NETWORK**

According to VMWare, A network sandbox is an isolated testing environment that enables security teams to observe, analyze, detect, and block suspicious artifacts traversing the network. [Read More](https://www.vmware.com/topics/network-sandbox)

**USE OF SANDBOX NETWORK**

**KEY FEATURES**
- An Ubuntu desktop connected to a gateway server interface
- An Ubuntu WordPress server connected to another interface of the gateway
- An Interface of the gateway configure for internet access using the Ubuntu Desktop
- An active communication between the Ubuntu desktop which is on one subnet and the Ubuntu WordPress server on another subnet.

**REQUIREMENT**
* An Ubuntu Desktop VM
* A Gateway Router (Here we are using an Ubuntu Server)
* An Application Server (Here we are using an Ubuntu WordPress server)

**PROCESS**
- [Download and Install an Ubuntu Destop VM](_posts/2024-11-08-ubuntuvmsetup.md)
- Download and Install an Ubuntu Server 
- Download and Install an Ubuntu WordPress server

**CONFIGURING YOUR UBUNTU DESKTOP VM** <br>
The Ubuntu Desktop will be configured to have a static IP address running in an internal network.

**On your Ubuntu Desktop click on the network icon**

**CONFIGURING YOUR UBUNTU SERVER (GATEWAY)** <br>

**CONFIGURING YOUR UBUNTU WORDPRESS SERVER**
