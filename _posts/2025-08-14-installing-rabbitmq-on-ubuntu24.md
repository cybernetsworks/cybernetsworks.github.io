---
layout: post
title:  "Installing RabbitMQ on Ubuntu24.04"
author: Snowdiamond
categories: [ SOC]
image: /assets/images/opencti/opencti.jpg
tags: [SOC]
---
**INTSTALLING RABBITMQ ON UBUNTU24**

**RABITMQ**

**INSTALLATION PROCESS**

- Run system update
- Install Dependeces 
- Add Repository Signing Key
- Add a Repository (Apt Source List) File
- Enable Management-plugin
- Enable and Start rabbitmq-server

**STEP ONE:** Run an update

```
sudo apt update
```

**STEP TWO:** Install Dependences

```
sudo apt-get install curl gnupg -y
```
- Enable apt HTTPS Transport
```
sudo apt-get install apt-transport-https
```

**STEP THREE:** Add Repository Signing Key

```
sudo apt-get install curl gnupg apt-transport-https -y

## Team RabbitMQ's signing key
curl -1sLf "https://keys.openpgp.org/vks/v1/by-fingerprint/0A9AF2115F4687BD29803A206B73A36E6026DFCA" | sudo gpg --dearmor | sudo tee /usr/share/keyrings/com.rabbitmq.team.gpg > /dev/null
```

**STEP FOUR:** Add a Repository (Apt Source List) File
```
sudo tee /etc/apt/sources.list.d/rabbitmq.list <<EOF
```
- Enter the following lines 
```
## Modern Erlang/OTP releases
##
deb [arch=amd64 signed-by=/usr/share/keyrings/com.rabbitmq.team.gpg] https://deb1.rabbitmq.com/rabbitmq-erlang/ubuntu/noble noble main
deb [arch=amd64 signed-by=/usr/share/keyrings/com.rabbitmq.team.gpg] https://deb2.rabbitmq.com/rabbitmq-erlang/ubuntu/noble noble main

## Provides modern RabbitMQ releases
##
deb [arch=amd64 signed-by=/usr/share/keyrings/com.rabbitmq.team.gpg] https://deb1.rabbitmq.com/rabbitmq-server/ubuntu/noble noble main
deb [arch=amd64 signed-by=/usr/share/keyrings/com.rabbitmq.team.gpg] https://deb2.rabbitmq.com/rabbitmq-server/ubuntu/noble noble main
EOF
```
press *enter*

**STEP FiIVE:** Update and install the redis package
```
sudo apt-get update -y
```

- Install Erlang packages
```
sudo apt-get install -y erlang rabbitmq-server
```

**STEP SIX:** Enable Management-plugin
```
sudo rabbitmq-plugins enable rabbitmq_management
```

**STEP SEVEN:** Enable and Start rabbitmq-server
```
sudo systemctl enable rabbitmq-server
sudo systemctl start rabbitmq-server
```