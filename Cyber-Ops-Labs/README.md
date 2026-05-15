# CCNA 1 Lab 3: Multi-Subnet DHCP Allocation and HTTP Service Routing

![Cisco](https://img.shields.io/badge/Cisco-Packet_Tracer-049fd9?style=for-the-badge&logo=cisco&logoColor=white)
![DHCP](https://img.shields.io/badge/Services-DHCP-orange?style=for-the-badge)
![HTTP](https://img.shields.io/badge/Protocols-HTTP-success?style=for-the-badge)

## Abstract
This laboratory demonstrates the configuration of a central router to manage multiple broadcast domains. The objective is to implement dual Dynamic Host Configuration Protocol (DHCP) pools on a single router, providing distinct network parameters to separate subnets, and to verify inter-subnet routing by accessing a dedicated HTTP web server.

## Topology Architecture
![Network Topology](37.png)

The infrastructure consists of two primary networks routed through a central gateway (Router0):
* *Client Subnet (1.0.0.0/8):* Connected via GigabitEthernet0/0 (Gateway IP: 1.0.0.1). Contains a Layer 2 switch (Switch1) and end-user clients (PC0, Laptop3).
* *Server Subnet (2.0.0.0/8):* Connected via GigabitEthernet0/1 (Gateway IP: 2.0.0.1). Contains a static infrastructure device, Server0, hosting HTTP services.

## Device Configurations

### Router0 (r1)
The router is configured with two distinct DHCP pools (Pool_test1 and Pool_test2) to dynamically assign IP addresses, default gateways, and DNS servers to the respective subnets while excluding the gateway interfaces from the leasing pool.

![Router DHCP Pool 1 Configuration](31.png)
![Router DHCP Pool 2 Configuration](32.png)

text
hostname r1
!
ip dhcp excluded-address 1.0.0.1
ip dhcp excluded-address 2.0.0.1
!
ip dhcp pool Pool_test1
 network 1.0.0.0 255.0.0.0
 default-router 1.0.0.1
 dns-server 1.0.0.1
!
ip dhcp pool Pool_test2
 network 2.0.0.0 255.0.0.0
 default-router 2.0.0.1
 dns-server 2.0.0.1


### Server0 (HTTP Server)
The server is statically assigned an IP address within the 2.0.0.0/8 subnet to ensure consistent reachability for web services.

![Server0 Static Configuration](34.png)

## Verification and Testing

1. *Dynamic Addressing Validation:* Client machines successfully leased IPv4 configurations from the router's Pool_test1 scope.
    * PC0 acquired 1.0.0.3/8
    ![PC0 IP Configuration](35.png)
    * Laptop3 acquired 1.0.0.2/8
    ![Laptop3 IP Configuration](36.png)

2. *Inter-Subnet Routing (ICMP):* Packet Tracer simulation verifies successful bi-directional ICMP echo requests and replies across the router between the 1.0.0.0/8 client subnet and the 2.0.0.0/8 server subnet.
    ![ICMP Simulation Verification](38.png)

3. *Application Layer Connectivity (HTTP):* Successful resolution and rendering of the HTTP service hosted on Server0 via port 80, confirming application-level accessibility across the routed infrastructure.
    ![HTTP Web Service Verification](39.png)
