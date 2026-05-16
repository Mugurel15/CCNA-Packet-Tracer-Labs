# Cybersecurity Lab 2: Anonymous FTP Access and Data Enumeration

![Kali Linux](https://img.shields.io/badge/OS-Kali_Linux-557C94?style=for-the-badge&logo=kali-linux&logoColor=white)
![FTP](https://img.shields.io/badge/Protocol-FTP-1679A7?style=for-the-badge)
![Enumeration](https://img.shields.io/badge/Phase-Enumeration-darkred?style=for-the-badge)

## Abstract
This repository documents the identification and exploitation of a misconfigured File Transfer Protocol (FTP) service. The laboratory demonstrates how anonymous authentication can be leveraged to access remote file systems, perform directory enumeration, and identify exposed sensitive cryptographic assets.

## Environment Setup
* *Operating System:* Kali Linux (VirtualBox Virtual Machine)
* *Target IP Address:* 192.168.14.18
* *Target Service:* FTP (TCP Port 21, GNU inetutils 2.8)
* *Tools Utilized:* ftp client, THC-Hydra (aborted)

## Execution Phases

### 1. Initial Assessment and Authentication
An initial brute-force attack via THC-Hydra was initiated against the FTP service but was manually interrupted. Manual testing proceeded to verify if the server permitted unauthenticated or anonymous access. 

Using the standard command-line FTP client, a connection was established to 192.168.14.18. Authentication was successfully bypassed by supplying the username anon and a blank password.

![Anonymous FTP Authentication](/images/cyber(3).png)

bash
ftp 192.168.14.18
Name (192.168.14.18:kali): anon
331 Password required for anon.
Password:
230 User anon logged in.


### 2. Directory Enumeration and Asset Discovery
Upon successful login, the session was switched to binary mode. A directory listing was requested to map the remote user's home directory.

![FTP Directory Listing](/images/cyber(4).png)

## Verification and Analysis
1. *Access Confirmed:* The FTP daemon (GNU inetutils 2.8) is improperly configured, granting shell-level read access to the anon user directory without requiring valid credential pairs.
2. *Data Exposure:* The enumeration phase revealed critical misconfigurations regarding file permissions and storage. High-value sensitive files, specifically sshkey.pem and sshkey.private, are publicly readable within the anonymous user's root directory. These cryptographic keys present an immediate vector for privilege escalation and secure shell (SSH) compromise on the target host.
