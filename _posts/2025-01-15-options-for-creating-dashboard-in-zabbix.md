---
layout: post
title:  "Setting Up a MacOS Virtual Machine"
author: Snowdiamond
categories: [ Jekyll, tutorial ]
image: assets/images/13.jpg
tag: [featured]
---

##SETTING UP A MACOS VM

Setting up a MacOs virtual machine requires two major steps
Creating the MacOS image
Setting up the virtual machine 

**CREATING THE MACOS IMAGE 


**SETTING UP THE VIRTUAL MACHINE 

Open your virtual box

```
Click New
Enter a name for your MacOS virtual machine.
Select the created Sonoma iso
Ensure you set type as "Mac OS X"
Click "Next"

```
!["MacOs VM Installation"](/assets/images/macos/macos-vm-setup-1.png)

Memory and CPU Allocation

```
Click and drag the Base Memory
Click and drag the Processor
Enable EFI
```
!["MacOs VM Installation"](/assets/images/macos/macos-vm-setup-2.png)

Assign Virtual Hard Disk Size

```
Click or manually access hard disk size
```
!["MacOs VM Installation"](/assets/images/macos/macos-vm-setup-3.png)

Configuration Review

```
Review Configuration
Click "Finish" 
```
!["MacOs VM Installation"](/assets/images/macos/macos-vm-setup-4.png)

**EDIT THE MACOS SETTING**

System Settings

```
Click on "Settings"
Uncheck "Floppy"
Set Chipset as "ICH9"

```
!["MacOs VM Installation"](/assets/images/macos/macos-vm-setup-5.png)

Display Settings

```
Assign atleast 128MB of video memory 
Enable 3D acceleration

```
!["MacOs VM Installation"](/assets/images/macos/macos-vm-setup-6.png)

Network Settings

```
Click on "Network"
Leave the first Adapter as "NAT"
Set the second as "Bridged"
```
!["MacOs VM Installation"](/assets/images/macos/macos-vm-setup-7.png)
!["MacOs VM Installation"](/assets/images/macos/macos-vm-setup-8.png)

USB Setting

```
Click on "USB"
Select USB 3.0 (xHCI) Controller
```
!["MacOs VM Installation"](/assets/images/macos/macos-vm-setup-9.png)

**PATCHING THE VIRTUAL MACHINE**
Exit Virtual Box before running the process.

```
Click on windows or press the windows button
type cmd
run as administrator

```
navigate to virtual machine directory using 
```
cd c/program files/oracle/virtualbox/
```

!["MacOs VM Installation"](/assets/images/macos/macos-vm-setup-10.png)

**NOTE:** Navigate to the right directory if you have installed the virtualbox in another location

Install the patch 

For Intel Processor run the following commands 

```
VBoxManage.exe modifyvm "VM Name" –-cpuidset 00000001 000106e5 00100800 0098e3fd bfebfbff
VBoxManage setextradata "VM Name" VBoxInternal/Devices/efi/0/Config/DmiSystemProduct “MacBookPro15,1”
VBoxManage setextradata "VM Name" "VBoxInternal/Devices/efi/0/Config/DmiBoardProduct" "Mac-551B86E5744E2388"
VBoxManage setextradata "VM Name" "VBoxInternal/Devices/smc/0/Config/DeviceKey" "ourhardworkbythesewordsguardedpleasedontsteal(c)AppleComputerInc"
VBoxManage setextradata "VM Name" "VBoxInternal/Devices/smc/0/Config/GetKeyFromRealSMC" 1

```

For AMD Processor Users

```
VBoxManage.exe modifyvm "VM Name" --cpuidset 00000001 000106e5 00100800 0098e3fd bfebfbff
VBoxManage setextradata "VM Name" "VBoxInternal/Devices/efi/0/Config/DmiSystemProduct" "iMac11,3"
VBoxManage setextradata "VM Name" "VBoxInternal/Devices/efi/0/Config/DmiSystemVersion" "1.0"
VBoxManage setextradata "VM Name" "VBoxInternal/Devices/efi/0/Config/DmiBoardProduct" "Iloveapple"
VBoxManage setextradata "VM Name" "VBoxInternal/Devices/smc/0/Config/DeviceKey" "ourhardworkbythesewordsguardedpleasedontsteal(c)AppleComputerInc"
VBoxManage setextradata "VM Name" "VBoxInternal/Devices/smc/0/Config/GetKeyFromRealSMC" 1
VBoxManage modifyvm "VM Name" --cpu-profile "Intel Core i7-6700K"
```