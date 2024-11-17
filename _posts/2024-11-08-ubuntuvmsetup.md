---
layout: post
title: "SETTING UP AN UBUNTU DESKTOP VM"
author: Snowdiamond
categories: [post]
tags: [vm]
image: assets/images/UbuntuDesktop-Interface.png
---
**REQUIREMENT**
* VirtualBox 
* Ubuntu Desktop VM. Download the Ubuntu Desktop VM iso from the official Ubuntu [website](https://ubuntu.com/download/desktop).

- Launch VirtualBox and click *NEW*
  ![Alt text](/assets/images/UbuntuDesktopInstall0.png)
- Fill in the requirements
  ![Alt text](/assets/images/UbuntuDesktopInstall1.png)
  - Enter a desired name. Here we used *UbuntuDesktop*
  - Select the Ubuntu Desktop image from the stored folder. Here it is in the Download Folder
  - Check Skip Unattended Installation
  - Click Next
- Memory Allocation
  ![Alt text](/assets/images/UbuntuDesktopInstall2.png)
  ![Alt text](/assets/images/UbuntuDesktopInstall3.png)
   **Possible Error**
    - Ubuntu 24.04 is not starting completely
    - Ubuntu 24.04 is freezing or working slow.
   **Reason**
    Ubuntu 24.04 unlike other versions requires more memory to function correctly. <br>
    You'll need to increase the base memory more if the issue persists after using the one suggested in the above screenshot.
- Finalization
  ![Alt text](/assets/images/UbuntuDesktopInstall4.png)
  Confirm the setup and click *Finish* to complete the process.

  ###INSTALLING UBUNTU DESKTOP
  On your VirtualBox click *start* to start the Ubuntu Desktop VM and installation process.
  ![Sart VM](/assets/images/UbuntuDesktopInstall5.png)
  Select *Try or Install Ubuntu*
  ![Select Ubuntu](/assets/images/UbuntuDesktopInstall6.png)
  Select *Install Ubuntu* and click *Next*
  ![Select Ubuntu](/assets/images/UbuntuDesktopInstall7.png)
  Select *Interactive installation*
  ![Select Ubuntu](/assets/images/UbuntuDesktopInstall8.png)
  Select *Default Selection*
  ![Select Ubuntu](/assets/images/UbuntuDesktopInstall9.png)
  This next step is optional.
  ![Select Ubuntu](/assets/images/UbuntuDesktopInstall10.png)
  Select *Erase disk and install Ubuntu*
  ![Select Ubuntu](/assets/images/UbuntuDesktopInstall11.png)
  Proceed to reviewing your installation choice
  ![Select Ubuntu](/assets/images/UbuntuDesktopInstall12.png)
  Restart Ubuntu Desktop to complete the setup process.
  ![Select Ubuntu](/assets/images/UbuntuDesktopInstall14.png)
