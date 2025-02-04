---
layout: post
title:  "NMAP FOR BEGINNERS"
author: Sal
categories: [ Networking ]
image: assets/images/nmap-logo.jpg
tag: [Networking]
---
**ABOUT NMAP**

NMAP (Network Mapper) is an open-source tool for network discovery and security auditing.

**SCANNING WITH NMAP**

BASIC HOST SCAN

- Ping Scan (Entire subnet scan)
```
nmap -sn target ip address. 
Example: nmap -sn 192.168.3.1-255
```
![nmap](/assets/images/nmap/nmap-sn-scan.png)

- Single Host Scan
```
nmap -sn 192.168.3.2
```
![nmap single](/assets/images/nmap/nmap-single-scan.png)

- Multiple Hosts Scan
```
nmap 192.168.3.1 192.168.3.2 192.168.3.3
```
![nmap multiple scan](/assets/images/nmap/nmap-multiple-scan.png)

- Subnet Scan
```
nmap 192.168.3.0/24
```
![nmap subnet scan](/assets/images/nmap/nmap-subnet-scan.png)
The result implies that all 1000 ports on the first host exists but they are closed and not accepting connection. Hence, the reason for the "reset" response.
The "ignored" message in this case could also be because the ports are filtered by a firewall.

**Explanation**

- The entire subnet scan will return the status (up or down) of all the host on that subnet
- The single scan will return status (up or down) of a single host
- The multiple host scan will return the status of the specified hosts (up and down). In this case, two of the hosts are up and one of the host has ssh and NRPE (Nagios Remote Plugin Executor) running.

PORT SCANNING 

- SYN Scan (Default) - Stealthier and widely used scan
```
nmap -sS 192.168.3.1
```
![nmap sync scan](/assets/images/nmap/nmap-syn-scan.png)
The result revealed the currently open tcp ports (22, 5666) running ssh and nagios respectively.
998 tcp ports are currently closed.

- TCP Connect Scan - A fast but more detectable scan
```
nmap -sT 192.168.3.1
```
![nmap TCP Scan](/assets/images/nmap/nmap-tcp-scan.png)
The result revealed the currently open tcp ports (22, 5666) running ssh and nagios respectively.
998 tcp ports are currently closed. This only scans for TCP Ports.

- UDP Connect Scan - UDP Ports discovery
```
nmap -sU 192.168.3.1
```
![nmap UDP Scan](/assets/images/nmap/nmap-udp-scan.png)

The result returned 999 closed udp ports and only one (5353) opened port.

- Port Range Scan - Use when you don't need to scan all ports
```
nmap -p 1-10 192.168.3.1
```
![nmap port range](/assets/images/nmap/nmap-port-range-scan.png)
The result returns the state of 10 ports and the services running on them.

- Specific Ports Scan - For a more specified port
```
nmap -p 22,80,443 192.168.3.1
```
![nmap specific port scan](/assets/images/nmap/nmap-specific-port-scan.png)
The result returns the state of port 22,80,443 and the services running on them.

SERVICE VERSION DETECTION
```
nmap -sV 192.168.1.1
```
![Service Version Detection](/assets/images/nmap/nmap-service-scan.png)
This scan returns the version details of software running on open ports.
In this case we have details for port 22 and 5666.

OPERATING SYSTEM DETECTION

```
nmap -O 192.168.3.1
```
![Operating System Detection](/assets/images/nmap/)
The result reveals the host is currently running a Linux operating system with kernel version between 4.15-5.8.

AGGRESSIVE SCAN 
This scan combines OS detection, version detection, script scanning and traceroute.
```
nmap -A 192.168.200.4
```
![nmap aggressive scan](/assets/images/nmap/nmap-aggressive-scan.png)

NMAP SCRIPTING ENGINE

