---
layout: post
title:  "Integrating Cortex and MISP with Thehive"
author: Snowdiamond
categories: [ Security Automation]
image: /assets/images/cortex/cortex-logo.jpg
tags: [Security Automation]
---
**INTEGRATING CORTEX MISP AND THEHIVE**

**REQUIREMENT**
- Active theHive VM
- Active Cortex VM
- Active MISP VM.

STEPS

**VM SETUP**

- [Install theHive4 on CentOS](https://cybernetsworks.github.io/installing-thehive-on-centos-7/)
- [Install cortex on an Ubuntu22.04 server](https://cybernetsworks.github.io/installing-cortex-on-ubuntu22.4/)
- [Install MISP on an Ubuntu24.04](https://cybernetsworks.github.io/install-misp-on-ubuntu22.4/) a prebuilt VM 

**INTEGRATION**

**MISP SETUP FOR INTEGRATION**

Create an Organization on MISP

```
Click "administration"
Click "add organization"
```
!["organization"](/assets/images/misp/organization-creation-1.png)

```
Enter Organization details as desired
Generate UID
Click "Submit"
```
!["organization"](/assets/images/misp/organization-creation-2.png)

Add User to Organization

```
Click on "administration"
Click "Add user"
```
!["User"](/assets/images/misp/adding-user-to-organization-1.png)

```
Enter User details
Chose the created organization
Set user role
click "create"
```
NOTE: It is required that you enter your login in an email format. example:user@organizationname.local

Create an Auth Key

```
Click on the view icon at the righthand side of the user
```
!["Auth key"](/assets/images/misp/AuthKey-1.png)

```
Click on "Auth Keys"
Click "Add Authentication Key"
```
!["Auth Key"](/assets/images/misp/Authkey-2.png)

```
Enter details as desired.
click submit.
```
!["Auth key"](/assets/images/misp/Authkey-3.png)
Depending on desired level of restriction, you can add allowed IP addresses and also set a read only token.

NOTE: Setting a read-only will determine if user can only read content on MISP or also post on it.

```
Copy the displayed authkey
Click "I have noted down my key, take me back now"
```
!["Auth Key"](/assets/images/misp/Authkey-4.png)


**CORTEX SETUP FOR INTEGRATION**
Add Organization

```
Click "Add organization"
```
!["Cortex-organization"](/assets/images/cortex/Organization-1.png)

```
Enter Organization details
```
!["Cortex-organization"](/assets/images/cortex/Organization-2.png)


Add User
```
Click on "users"
Click "Add users"
```
!["Cortex-user"](/assets/images/cortex/cortex-user-1.png)

```
Enter the user details as desired
Click "save user"
```
!["Cortex-user"](/assets/images/cortex/cortex-user-2.png)

Create user password

```
Click "new password"
Enter password
press Enter.
```
!["Cortex-user password"](/assets/images/cortex/cortex-user-3.png)

Create API Key
```
Click "Create API Key"
```
!["Cortex-API-Key"](/assets/images/cortex/cortex-user-api.png)

```
Click "reveal" to see the API Key
```
!["Cortex-API-Key"](/assets/images/cortex/user-api-2.png)

**INTEGRATION ON THEHIVE**

**Add Cortex Integration to thehive configuration file** 

Enter this comand
```
sudo nano /etc/thehive/application.conf
```

Add the following configuration

```
play.modules.enabled += org.thp.thehive.connector.cortex.CortexModule
cortex {
 Servers: [
    {
        name: "CORTEX-SERVER" 
        url: "<cortex ip address>:9001"
        auth {
            type: "bearer"
            key: "<put the generated key here>"
        }
        wsConfig {}
    }
 ]
}
```

**Add MISP Integration**
```
play.modules.enabled += org.thp.thehive.connector.misp.MispModule
misp {
 interval: 1 hour
 Servers: [
    {
        name = "MISP" 
        url =  "<cortex ip address/>"
        auth {
            type =  "bearer"
            key =  "<put the generated key here>"
        }
        wsConfig {}
            wsConfig.ssl.loose.acceptAnyCertificate: true
            tags = ["misp"]
            caseTemplate = "MISP-EVENT"
    }
 ]
}
```
Confirm Both MISP and Cortex Integration

```
Login to Hive dashboard
Click on the Username at the top right corner
Click about
```
!["Integration confirmed"](/assets/images/thehive/integration-confirmation.png)