---
layout: posts
title: "INSTALLING UBUNTU DESKTOP VM"
last_modified_at: 2024-11-09T16:20:02-05:00
image: /assets/images/UbuntuDesktopInstall10.png
categories:
  - Blog
tags:
  - Post Formats
  - readability
  - standard
---
If you are yet to setup your Ubuntu Desktop VM on your Virtual Box click [here](/_posts/2024-11-08-ubuntuvmsetuo.md) to do so before proceeding. 
- Select the Ubuntu vm on virtualbox and click **START** to proceed.
> ![Alt text](/assets/images/UbuntuDesktopInstall5.png)

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

