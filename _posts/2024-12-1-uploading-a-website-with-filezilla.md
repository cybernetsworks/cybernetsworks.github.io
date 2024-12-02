---
layout: post
title:  "Uploading a Website with Filezilla"
author: sal
categories: [ Jekyll, tutorial ]
image: assets/images/Filezilla-logo.png
---
### FILEZILLA
Filezilla 
download filezilla [here](https://filezilla-project.org/)

**UPLOADING A WEBSITE IN 3 STEPS**

- STEP ONE: Connect with your host. This step involves entering the following details in the quickconnect bar
  - Hostname: example.org
  - Username: Page
  - Password: 89hgnsdk
    
    NOTE: In this example, we are connecting with an Ubuntu 16.04 server and the protocol requires sftp.
    
- STEP TWO: Navigate to the right directory for the website. In this example it is /var/www.
- STEP THREE: Drag and drop files from your local machine to the connected host.
  ![alt image](/assets/images/Filezilla-connection.png)
