---
layout: post
title:  "Setting Up a Windows10 Virtual Machine"
author: Snowdiamond
categories: [ Jekyll, tutorial ]
image: assets/images/windows-logo.jpg
tag: [featured]
---

##SETTING UP A WINDOWS 10 VM

Creating windows10 virtual machine requires the following steps 

- Creating an iso file using the [media creation tool](https://www.microsoft.com/en-us/software-download/windows10)
- Setting up the virtual machine
- Installing the Operating system


**WINDOWS 10 ISO CREATION**
```
Run the downloaded media creation tool as an administrator
click accept to proceed 
```
![windows10 creation](/assets/images/window10/win10-vm-1.png)

```
select "Create an installation media (USB flash drive, DVD, or ISO file) for another PC
click "Next"
```
![windows10 creation](/assets/images/windows10/win10-vm-2.png)

```
Ensure windows 10 is selected
click "Next"
```
![windows10 creation](/assets/images/windows10/win10-vm-3.png)

```
Select "iso file"
click "Next"
```
![windows10 creation](/assets/images/windows10/win10-vm-4.png)

```
Select the location to save the file 
Wait for the file creation to complete
click "finish" upon completion
```

**VM SETUP ON VIRTUAL BOX**

Open your virtual box
```
Click on "New"
Enter the name of the VM
Select the iso file created as ISO image
Check "Skip Unattended Installation (This gives you more controll over the process)
```
![Windows10 creation](/assets/images/windows10/win10-vm-5.png)

Base Memory Allocation

```
Give the vm sufficient base memory
Allocate at least 2gig ram
click "Next"
```
![Windows10 creation](/assets/images/windows10/win10-vm-6.png)

Virtual Hard Disk SetUp

```
Assign enough disk size. Atleast 50gig. We are using 80gig in this example
Click "Next"
```
![Windows10 creation](/assets/images/windows10/win10-vm-7.png)

Review and Complete Setup

```
Review the setup
Click "finish" to complete the setup.
```
Motherboard Configuration
```
Click "Settings"
Click "System"
Click "Motherboard"
Uncheck "floppy" in Boot order.
```
![Windows10 creation](/assets/images/windows10/win10-vm-9.png)

Processor Setup

```
Click on "Processor"
Enable PAE/NX
```
![Windows10 creation](/assets/images/windows10/win10-vm-10.png)

Display Settings 

```
Click "Display"
Drag the video memory to 128MB (minimum) 
Check "Enable 3D Acceleration"
```
![Windows10 creation](/assets/images/windows10/win10-vm-11.png)

Network Configuration

```
Enable two network interfaces 
Set the first as "NAT" to permit internet access.
set the second as "Bridged" to allow access to the local network
```
**OPERATING SYSTEM INSTALLATION**

Start The Virtual Machine to Begin The Operating System Installation

Language Selection

```
Select your desired language
Select your desired Time and currency format
click next
```
![Windows10 creation](/assets/images/windows10/win10-vm-12.png)

```
Click "Next" when the window appears
Select "I don't have the product key"
```
![Windows10 creation](/assets/images/windows10/win10-vm-13.png)

Windows Package Selection

```
Select your desired Windows10 package. We are using Windows10 pro here.
```
![Windows10 creation](/assets/images/windows10/win10-vm-14.png)

Licence Agreement 
```
Accept the license agreement
```
![Windows10 creation](/assets/images/windows10/win10-vm-15.png)

Installation Type Selection
```
Select Custom option
```
![Windows10 creation](/assets/images/windows10/win10-vm-16.png)

Drive Selection

```
Select the drive for installing the OS

Click "Next"
```
![Windows10 creation](/assets/images/windows10/win10-vm-17.png)

Wait for the installation process to run and finish

![Windows10 creation](/assets/images/windows10/win10-vm-18.png)

Completion

The VM will restart. When prompted to press any key to install from cd, ignore it.

```
Select your region 
click "Yes"
```
![Windows10 creation](/assets/images/windows10/win10-vm-19.png)

```
Select your desired keyboard layout
You can add a second keyboard layout(optional)
```
![Windows10 creation](/assets/images/windows10/win10-vm-20.png)

```
Select your desire setup
Enter your microsoft account email or create new one
Complete the puzzle challenge
Setup a pin
Skip or setup experience settings
```
