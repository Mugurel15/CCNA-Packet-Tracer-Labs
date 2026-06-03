# CCNA 1 Lab 4: Multi-Router IPv4/IPv6 Routing with RIP, Static IPv6 and Layer 2 Switching

![Cisco](https://img.shields.io/badge/Cisco-Packet_Tracer-049fd9?style=for-the-badge&logo=cisco&logoColor=white)
![IPv4](https://img.shields.io/badge/Protocol-IPv4-blue?style=for-the-badge)
![IPv6](https://img.shields.io/badge/Protocol-IPv6-success?style=for-the-badge)
![RIP](https://img.shields.io/badge/Routing-RIPv2-orange?style=for-the-badge)

## Abstract

This laboratory demonstrates the implementation of a hybrid IPv4 and IPv6 routed infrastructure using multiple Cisco routers in a triangular topology. The objective is to establish full end-to-end connectivity between remote networks using **RIPv2 for IPv4 dynamic routing** and **static routing for IPv6 communication**.

The lab also includes **Layer 2 switching** inside the local client subnet and validates network functionality through successful **ICMP communication** between end devices located on different networks.

---

## Topology Architecture

![Network Topology](/images/Screenshot%202026-06-03%20171045.png)

The infrastructure consists of three interconnected routers, one Layer 2 switch, and two end-user devices communicating through IPv4 and IPv6 routing.

![Network Topology Alternative View](/images/Screenshot%202026-06-03%20171053.png)

### Network Overview

### Client Network A (1.0.0.0/8)

Connected through **Switch0 (2960-24TT)** operating as a Layer 2 switching device.

| Device | Interface | IPv4 Address | IPv6 Address |
|---|---|---|---|
| Router0 | GigabitEthernet0/0 | 1.0.0.1 | 1::1/64 |
| Switch0 | VLAN1 | 1.0.0.2 | 1::2/64 |
| PC0 | FastEthernet0 | 1.0.0.3 | 1::3/64 |

**Default Gateway:** `1.0.0.1`  
**IPv6 Default Gateway:** `1::1`

---

### Transit Network 1 (2.0.0.0/8)

Serial connection between **Router0** and **Router2**.

| Device | Interface | IPv4 Address | IPv6 Address |
|---|---|---|---|
| Router0 | Serial0/3/0 | 2.0.0.1 | 2::1/64 |
| Router2 | Serial0/3/0 | 2.0.0.2 | 2::2/64 |

---

### Transit Network 2 (3.0.0.0/8)

GigabitEthernet connection between **Router0** and **Router1**.

| Device | Interface | IPv4 Address | IPv6 Address |
|---|---|---|---|
| Router0 | GigabitEthernet0/1 | 3.0.0.1 | 3::1/64 |
| Router1 | GigabitEthernet0/0 | 3.0.0.2 | 3::2/64 |

---

### Transit Network 3 (4.0.0.0/8)

GigabitEthernet connection between **Router1** and **Router2**.

| Device | Interface | IPv4 Address | IPv6 Address |
|---|---|---|---|
| Router1 | GigabitEthernet0/1 | 4.0.0.1 | 4::1/64 |
| Router2 | GigabitEthernet0/0 | 4.0.0.2 | 4::2/64 |

---

### Client Network B (5.0.0.0/8)

Directly connected to **Router2**.

| Device | Interface | IPv4 Address | IPv6 Address |
|---|---|---|---|
| Router2 | GigabitEthernet0/1 | 5.0.0.1 | 5::1/64 |
| PC1 | FastEthernet0 | 5.0.0.2 | 5::2/64 |

**Default Gateway:** `5.0.0.1`  
**IPv6 Default Gateway:** `5::1`

---

## Device Configurations

### Router0

Router0 acts as the main gateway for **Client Network A** and participates in both IPv4 dynamic routing and IPv6 static routing.

![Router0 Configuration Part 1](/images/Screenshot%202026-06-03%20171212.png)

![Router0 Configuration Part 2](/images/Screenshot%202026-06-03%20171219.png)

```text
hostname r1

ipv6 unicast-routing

interface GigabitEthernet0/0
 ip address 1.0.0.1 255.0.0.0
 ipv6 address 1::1/64
 no shutdown

interface Serial0/3/0
 ip address 2.0.0.1 255.0.0.0
 ipv6 address 2::1/64
 clock rate 64000
 no shutdown

interface GigabitEthernet0/1
 ip address 3.0.0.1 255.0.0.0
 ipv6 address 3::1/64
 no shutdown

ipv6 route 4::/64 3::2
ipv6 route 5::/64 2::2

router rip
 version 2
 network 1.0.0.0
 network 2.0.0.0
 network 3.0.0.0
 no auto-summary
```

---

### Router1

Router1 acts as a transit router between Router0 and Router2.

![Router1 Configuration](/images/Screenshot%202026-06-03%20171240.png)

```text
ipv6 unicast-routing

interface GigabitEthernet0/0
 ip address 3.0.0.2 255.0.0.0
 ipv6 address 3::2/64
 no shutdown

interface GigabitEthernet0/1
 ip address 4.0.0.1 255.0.0.0
 ipv6 address 4::1/64
 no shutdown

ipv6 route 1::/64 3::1
ipv6 route 2::/64 3::1
ipv6 route 5::/64 4::2

router rip
 version 2
 network 3.0.0.0
 network 4.0.0.0
 no auto-summary
```

---

### Router2

Router2 provides gateway functionality for **Client Network B** and maintains connectivity with Router0 and Router1.

```text
ipv6 unicast-routing

interface Serial0/3/0
 ip address 2.0.0.2 255.0.0.0
 ipv6 address 2::2/64
 no shutdown

interface GigabitEthernet0/0
 ip address 4.0.0.2 255.0.0.0
 ipv6 address 4::2/64
 no shutdown

interface GigabitEthernet0/1
 ip address 5.0.0.1 255.0.0.0
 ipv6 address 5::1/64
 no shutdown

ipv6 route 1::/64 2::1
ipv6 route 3::/64 4::1

router rip
 version 2
 network 2.0.0.0
 network 4.0.0.0
 network 5.0.0.0
 no auto-summary
```

---

### Switch0 Configuration

Switch0 operates as a **Layer 2 switching device** inside the `1.0.0.0/8` client network. It connects PC0 to Router0 and uses VLAN1 for management.

![Switch0 Configuration](/images/Screenshot%202026-06-03%20171134.png)

```text
interface Vlan1
 ip address 1.0.0.2 255.0.0.0
 ipv6 address 1::2/64

ipv6 route ::/0 1::1
```

---

## End Device Configuration

### PC0 IPv4 / IPv6 Configuration

![PC0 IPv4 and IPv6 Configuration](/images/Screenshot%202026-06-03%20171103.png)

![PC0 Full IPv4 and IPv6 Configuration](/images/Screenshot%202026-06-03%20171110.png)

#### IPv4

```text
IP Address: 1.0.0.3
Subnet Mask: 255.0.0.0
Default Gateway: 1.0.0.1
DNS Server: 0.0.0.0
```

#### IPv6

```text
IPv6 Address: 1::3/64
Default Gateway: 1::1
```

---

### PC1 IPv4 / IPv6 Configuration

![PC1 IPv4 and IPv6 Configuration](/images/Screenshot%202026-06-03%20171253.png)

#### IPv4

```text
IP Address: 5.0.0.2
Subnet Mask: 255.0.0.0
Default Gateway: 5.0.0.1
DNS Server: 0.0.0.0
```

#### IPv6

```text
IPv6 Address: 5::2/64
Default Gateway: 5::1
```

---

## Routing Implementation

### IPv4 Routing

IPv4 routing was implemented using **RIPv2**. Each router advertises only the directly connected IPv4 networks.

```text
router rip
 version 2
 network [directly-connected-network]
 no auto-summary
```

This allows the routers to dynamically learn remote IPv4 networks and forward packets between PC0 and PC1.

---

### IPv6 Routing

IPv6 connectivity was implemented using manually configured **static IPv6 routes**.

Example:

```text
ipv6 route 5::/64 2::2
```

Static routes were configured so each router could reach the remote IPv6 prefixes that were not directly connected.

---

## Verification and Testing

### 1. Interface Status Verification

All router interfaces were enabled with the `no shutdown` command.

```text
show ip interface brief
```

Expected result:

```text
Interface              IP-Address      Status      Protocol
GigabitEthernet0/0     up              up
GigabitEthernet0/1     up              up
Serial0/3/0            up              up
```

---

### 2. IPv4 Routing Table Verification

RIPv2 routes can be checked using:

```text
show ip route
```

The routing table should contain directly connected networks and RIP-learned remote networks.

---

### 3. IPv6 Routing Table Verification

IPv6 routes can be checked using:

```text
show ipv6 route
```

The output should include connected IPv6 networks and static routes.

---

### 4. End-to-End ICMP Connectivity

Packet Tracer simulation verified successful ICMP communication between **PC0** and **PC1**.

![ICMP Simulation Verification](/images/Screenshot%202026-06-03%20171352.png)

#### IPv4 Test from PC0 to PC1

```text
ping 5.0.0.2
```

#### IPv6 Test from PC0 to PC1

```text
ping 5::2
```

Successful ICMP replies confirm that routing is working correctly between the two remote LANs.

---

## Repository Structure


---

## Screenshot Reference

| File Name | Description |
|---|---|
| `Screenshot 2026-06-03 171045.png` | Main network topology |
| `Screenshot 2026-06-03 171053.png` | Alternative topology view |
| `Screenshot 2026-06-03 171103.png` | PC0 IPv4 and IPv6 configuration |
| `Screenshot 2026-06-03 171110.png` | PC0 full IPv4 and IPv6 configuration |
| `Screenshot 2026-06-03 171134.png` | Switch0 configuration |
| `Screenshot 2026-06-03 171212.png` | Router0 configuration part 1 |
| `Screenshot 2026-06-03 171219.png` | Router0 configuration part 2 |
| `Screenshot 2026-06-03 171240.png` | Router1 configuration |
| `Screenshot 2026-06-03 171253.png` | PC1 IPv4 and IPv6 configuration |
| `Screenshot 2026-06-03 171352.png` | ICMP simulation verification |

---

## Conclusion

This laboratory successfully demonstrated the implementation of a **multi-router IPv4/IPv6 network** using Cisco Packet Tracer.

The IPv4 side of the network was configured using **RIPv2 dynamic routing**, while IPv6 connectivity was implemented using **static IPv6 routes**. The local client network also included a **Layer 2 switch**, which provided switching functionality between PC0 and Router0.

Final ICMP testing confirmed successful end-to-end communication between **PC0** and **PC1**, proving that routing and addressing were configured correctly across all network segments.
