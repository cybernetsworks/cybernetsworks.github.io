---
layout: post
title:  "Installing Redis on Ubuntu24.04"
author: Snowdiamond
categories: [ SOC]
image: /assets/images/opencti/opencti.jpg
tags: [SOC]
---
**INTSTALLING REDIS ON UBUNTU24**

**REDIS**

**INSTALLATION PROCESS**

- Run system update
- Install helper tools
- Download the Redis GPG key
- Set correct permissions for the GPG key
- Add the Redis APT repository
- Update and install the redis package
- Enable and start redis-server

**STEP ONE:** Run an update

```
sudo apt update
```

**STEP TWO:** Install helper tools

```
sudo apt-get install lsb-release curl gpg
```

**STEP THREE:** Download Redis GPG key

```
curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg
```

**STEP FOUR:** Set correct permissions for the GPG key
```
sudo chmod 644 /usr/share/keyrings/redis-archive-keyring.gpg
```

**STEP FIVE:** Add the Redis APT repository
```
echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list
```
**STEP SIX:** Update and install the redis package
```
sudo apt-get update
sudo apt-get install redis
```

**STEP SEVEN:** Enable and start the service
```
sudo systemctl enable redis-server
sudo systemctl start redis-server
``` 

