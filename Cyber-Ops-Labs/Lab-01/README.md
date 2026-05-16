# Cybersecurity Lab 1: SSH Brute-Force Simulation and Traffic Analysis

![Kali Linux](https://img.shields.io/badge/OS-Kali_Linux-557C94?style=for-the-badge&logo=kali-linux&logoColor=white)
![Wireshark](https://img.shields.io/badge/Tool-Wireshark-1679A7?style=for-the-badge&logo=wireshark&logoColor=white)
![Hydra](https://img.shields.io/badge/Tool-THC_Hydra-darkred?style=for-the-badge)

## Abstract
This repository documents a local simulation of a dictionary-based brute-force attack against a Secure Shell (SSH) service. The laboratory involves verifying the target service, establishing a packet capture to analyze the network footprint of the attack, and utilizing THC-Hydra to crack the target credentials via a standard wordlist.

## Environment Setup
* *Operating System:* Kali Linux (VirtualBox Virtual Machine)
* *Target IP Address:* 10.0.2.15 (Local NAT Interface)
* *Target Service:* OpenSSH server (TCP Port 22)
* *Tools Utilized:* systemctl, ping, Wireshark, THC-Hydra
* *Wordlist:* rockyou.txt

## Execution Phases

### 1. Service and Connectivity Verification
Prior to the attack simulation, the OpenSSH service status is validated to ensure the daemon is actively running and listening for incoming connections. External network connectivity is also confirmed via ICMP echo requests.

![SSH Service Status](/images/cyber2.jpeg)

bash
sudo systemctl status ssh
ping 1.1.1.1


### 2. Network Traffic Monitoring
Wireshark is initialized to capture traffic on the active network interface. A display filter (tcp.port == 22) is applied to isolate SSH communication. This allows for the observation of TCP handshakes and the high volume of encrypted packets generated during the authentication attempts.

![Wireshark Packet Capture](/images/cyber3.jpeg)

### 3. Brute-Force Execution
A dictionary attack is launched against the local SSH service using THC-Hydra. The attack targets the known username kali and iterates through the rockyou.txt payload to systematically guess the authentication phrase.

![Hydra Execution and Success](/images/cyber1.jpeg)

bash
hydra -l kali -P /usr/share/wordlists/rockyou.txt 10.0.2.15 ssh


## Verification and Analysis
1. *Compromise Confirmed:* The Hydra utility successfully completed the attack vector, identifying the valid credentials: login: kali and password: 12345678.
2. *Traffic Footprint:* The packet capture demonstrates a rapid succession of TCP connections (SYN, SYN-ACK, ACK), followed immediately by SSHv2 encrypted payload exchanges and connection resets (RST). This high-frequency connection cycling is the primary network signature of an automated brute-force attack.
