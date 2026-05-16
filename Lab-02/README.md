# CCNA 1 Lab 2: Basic Device Configuration and SSH Implementation

![Cisco](https://img.shields.io/badge/Cisco-Packet_Tracer-049fd9?style=for-the-badge&logo=cisco&logoColor=white)
![Security](https://img.shields.io/badge/Security-SSHv2-success?style=for-the-badge)
![DHCP](https://img.shields.io/badge/Services-DHCP-orange?style=for-the-badge)

## Abstract
This repository documents the configuration of a Cisco router to provide dynamic IP addressing and accept secure remote management connections. The laboratory focuses on initializing interface parameters, establishing a local DHCP pool, and securing Virtual Teletype (VTY) lines using Secure Shell (SSH) version 2 with local database authentication and RSA encryption.

## Topology Architecture
* *Gateway Device:* Router r1 acting as the default gateway and DHCP server for the local subnet.
* *Local Subnet (1.0.0.0/8):* Connected via GigabitEthernet0/0 (IP: 1.0.0.1).
* *Client Devices:* PC0 and PC2 utilized for SSH verification and remote management.

## Device Configurations

### Router (r1)
The following configuration applies the base system settings, configures the DHCP server, generates RSA crypto keys, and locks down remote access to SSH only.

![Router Configuration Phase 1](/images/11.png)
![Router Configuration Phase 2](/images/16.png)
![Router Configuration Phase 3](/images/18.png)

text
hostname r1
!
enable secret cisco
!
username cisco password cisco
!
ip domain-name Alex.ro
crypto key generate rsa
! (Key modulus size set to 2048 bits)
ip ssh version 2
!
ip dhcp excluded-address 1.0.0.1
ip dhcp pool r1
 network 1.0.0.0 255.0.0.0
 default-router 1.0.0.1
!
interface GigabitEthernet0/0
 ip address 1.0.0.1 255.0.0.0
 no shutdown
!
line vty 0 4
 login local
 transport input ssh


## Verification and Testing
1. *DHCP Validation:* Client endpoints successfully receive dynamic IPv4 configurations from the r1 pool.
    ![DHCP Setup Logs](/images/14.png)
    ![DHCP Setup Logs 2](/images/15.png)
2. *Secure Remote Access (SSH):* Verified remote connectivity from client endpoints to the router's gateway interface (1.0.0.1) using the SSH protocol. Successful authentication via the local user database and execution of privileged commands (show run).
    ![SSH Verification PC0](/images/19.png)
    ![SSH Verification PC2](/images/20.png)
