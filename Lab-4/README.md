## Topology Architecture
![Network Topology](/images/topology.png)

The infrastructure consists of three interconnected routers, a Layer 2 switch, and two remote end devices communicating through IPv4 and IPv6 routing.

### Network Overview

* **Client Network A (1.0.0.0/8):**
  * Connected through **Switch0 (2960-24TT)** operating as a Layer 2 switching device.
  * **Router0 GigabitEthernet0/0:** `1.0.0.1`
  * **Switch0 VLAN1:** `1.0.0.2`
  * **PC0:** `1.0.0.3`
  * Default Gateway: `1.0.0.1`

* **Transit Network 1 (2.0.0.0/8):**
  * Serial connection between **Router0** and **Router2**
  * **Router0 Serial0/3/0:** `2.0.0.1`
  * **Router2 Serial0/3/0:** `2.0.0.2`

* **Transit Network 2 (3.0.0.0/8):**
  * GigabitEthernet connection between **Router0** and **Router1**
  * **Router0 GigabitEthernet0/1:** `3.0.0.1`
  * **Router1 GigabitEthernet0/0:** `3.0.0.2`

* **Transit Network 3 (4.0.0.0/8):**
  * GigabitEthernet connection between **Router1** and **Router2**
  * **Router1 GigabitEthernet0/1:** `4.0.0.1`
  * **Router2 GigabitEthernet0/0:** `4.0.0.2`

* **Client Network B (5.0.0.0/8):**
  * Directly connected to **Router2**
  * **Router2 GigabitEthernet0/1:** `5.0.0.1`
  * **PC1:** `5.0.0.2`
  * Default Gateway: `5.0.0.1`
