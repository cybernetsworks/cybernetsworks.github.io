---
layout: post
title:  "Installing theHive on Ubuntu22.4"
author: Snowdiamond
categories: [ Security testing ]
image: assets/images/UbuntuDesktop-Interface.png
tags: [Networking]
---
**INSTALLING THEHIVE ON UBUNTU 22.4**

**THE HIVE**

AUTOMATED INSTALLATION

NOTE: For seamless result, run script on a freshly installed ubuntu 22.04 server.

See [HERE](https://cybernetsworks.github.io/setting-up-an-ubuntu-server-vm/) for details on how to install ubuntu 22.04 server vm.

**IMPORTANT**
For the script to run effectively, your server must meetup with the minimum requirements 
```
4 CPU
16 GB RAM
```

Open your Ubuntu 22.04 server and run the following commands.
```
sudo apt update
sudo apt upgrade
```
Run the automated script provided by theHive

```
wget -q -O /tmp/install.sh https://archives.strangebee.com/scripts/install.sh ; sudo -v ; bash /tmp/install.sh
```
A warning might pop up 

!["The hive installation"](/assets/images/thehive/automated-hive-install-2.png)
```
Type **yes**
```
In the installation option displayed, select ``Inatall thehive`` 
!["The hive installation"](/assets/images/thehive/automated-hive-install-3.png)

Allow the automated installation take place.
!["The hive installation"](/assets/images/thehive/automated-hive-install-1.png)

Access thehive dashboard using the provided login data

!["The hive installation"](/assets/images/thehive/automated-hive-install-4.png).