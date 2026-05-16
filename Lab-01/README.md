# CCNA 1 Lab 1: Layer 3 Routing and DHCP Configuration

![Cisco](https://img.shields.io/badge/Cisco-Packet_Tracer-049fd9?style=for-the-badge&logo=cisco&logoColor=white)
![Routing](https://img.shields.io/badge/Routing-Static-success?style=for-the-badge)
![DHCP](https://img.shields.io/badge/Services-DHCP-orange?style=for-the-badge)

## Abstract
This repository details the configuration and verification of a network topology utilizing a Cisco 2911 Router and a Cisco 3560-24PS Multilayer Switch. The primary objective is to establish end-to-end ICMP connectivity between two distinct subnets by implementing static routing, IP routing on a Layer 3 switch, and dynamic host configuration protocol (DHCP) services.

## Topology Architecture
![Network Topology](/images/1.png)

The network consists of three primary subnets connected via physical interfaces and routed ports:
* **Subnet A (1.0.0.0/8):** Hosts `PC0` (DHCP client). The default gateway is `Router0` (Gig0/0: `1.0.0.1`).
* **Transit Subnet (2.0.0.0/8):** Point-to-point link connecting `Router0` (Gig0/1: `2.0.0.1`) and `Multilayer Switch0` (Fa0/1: `2.0.0.2`).
* **Subnet B (3.0.0.0/8):** Hosts `PC1` (DHCP client). The default gateway is `Multilayer Switch0` (Fa0/2: `3.0.0.1`).

## Device Configurations

### Router0 (r1)
Responsible for acting as the DHCP server for Subnet A and routing traffic across the transit link via a static route.

![Router0 Configuration Part 1](/images/3.png)
![Router0 Configuration Part 2](/images/4.png)

```text
hostname r1
!
ip dhcp excluded-address 1.0.0.1
ip dhcp pool R1DHCP
 network 1.0.0.0 255.0.0.0
 default-router 1.0.0.1
 dns-server 1.0.0.1
!
interface GigabitEthernet0/0
 ip address 1.0.0.1 255.0.0.0
 duplex auto
 speed auto
!
interface GigabitEthernet0/1
 ip address 2.0.0.1 255.0.0.0
 duplex auto
 speed auto
!
ip route 3.0.0.0 255.0.0.0 2.0.0.2
```

### Multilayer Switch0 (sw1r2)
Operating as a Layer 3 routing device. It utilizes routed ports (`no switchport`), serves DHCP for Subnet B, and routes traffic back to Subnet A.

![Switch0 Configuration Part 1](/images/5.png)
![Switch0 Configuration Part 2](/images/6.png)

```text
hostname sw1r2
!
ip routing
!
ip dhcp excluded-address 3.0.0.1
ip dhcp pool sw_r2_DHCP
 network 3.0.0.0 255.0.0.0
 default-router 3.0.0.1
 dns-server 3.0.0.1
!
interface FastEthernet0/1
 no switchport
 ip address 2.0.0.2 255.0.0.0
 duplex auto
 speed auto
!
interface FastEthernet0/2
 no switchport
 ip address 3.0.0.1 255.0.0.0
 duplex auto
 speed auto
!
ip route 1.0.0.0 255.0.0.0 2.0.0.1
```

## Verification and Testing
1. **DHCP Allocation:** Validated that both endpoints successfully obtained IPv4 configurations dynamically.
    * `PC0` assigned `1.0.0.2/8`
    ![PC0 DHCP Configuration](/images/2.png)
    * `PC1` assigned `3.0.0.2/8`
    ![PC1 DHCP Configuration](/images/8.png)
2. **End-to-End Connectivity:** ICMP Echo Requests and Replies successfully traverse the Layer 3 boundaries. Packet Tracer PDU lists confirm bi-directional communication between `PC0` and `PC1` is operational.
    ![Connectivity Verification](/images/7.png)
