---
layout: post
title:  "CREATING A FORENSIC IMAGE WITH FTK IMAGER"
author: Sal
categories: [ Digital Forensic ]
image: assets/images/UbuntuDesktop-Interface.png
tags: [Forensic]
---
**CREATING A FORENSIC E01 IMAGE WITH FTK IMAGER**

**ABOUT FTK IMAGER**

**STEP**

Launch the FTK IMAGER application

!["FTK"](/assets/images/ftk/FTK-1.png)

DISK IMAGE CREATION

```
Connect the device to be imaged
Click "File"
Click "Create Disk Image"
Select "Physical Drive"
Click "Next"
```
!["FTK"](/assets/images/ftk/FTK-2.png)

**NOTE:** We are selecting physical drive so we can capture the entire disk.

```
Select the drive that will be imaged
Click "Finish"
```
    
!["FTK"](/assets/images/ftk/FTK-3.png)

IMAGE TYPE SELECTION

!["FTK"](/assets/images/ftk/FTK-4.png)

**NOTE:** Here, we are creating E01 image
```
Click "Add"
Select "E01"
Click "Next"
```
!["FTK"](/assets/images/ftk/FTK-5.png)

ADD INVESTIGATION DETAILS

!["FTK"](/assets/images/ftk/FTK-6.png)

LOCATION SELECTION

```
Select location to save file.
Set Image Fragement Size to "0" to keep entire disk image within a single file.
Set image file name
```
!["FTK"](/assets/images/ftk/FTK-7.png)

FINALIZING

Ensure option to
```
- Check "verify image after they are created" is selected."
- Check "Create directory listing of all files in the image after they are created"

```
!["FTK"](/assets/images/ftk/FTK-8.png)
START THE CREATION

```
Click "Start"
```
!["FTK"](/assets/images/ftk/FTK-9.png)

INTEGRITY VERIFICATION

```
Compare the hash generated. If they are not thesame, you need to repeat the processs.
```

!["FTK"](/assets/images/ftk/FTK-10.png)

**YOU CAN ALSO CLICK ON "IMAGE SUMMARY" TO SEE COMPLETE IMAGE DETAILS.**

**ADDING AN IMAGE TO FTK**

```
Click "File"
Click "Add"
Select Source (Image in this case)
```
!["FTK"](/assets/images/ftk/FTK-11.png)

```
Select the image to Add
click "finish"
```
!["FTK"](/assets/images/ftk/FTK-12.png)

