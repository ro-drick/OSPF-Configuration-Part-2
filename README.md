## OSPF Packet Tracer Lab

### 1. **Network Diagram Overview**
This lab configures OSPF (Open Shortest Path First) on a network consisting of four routers (R1, R2, R3, R4), one switch (SW1), and a PC (PC1). The goal is to ensure routing via OSPF, advertise a default route using an ASBR (Autonomous System Boundary Router), and configure loopback interfaces.

<img src= "https://github.com/ro-drick/OSPF-Configuration-Part-2/blob/main/ospf-2.PNG">

#### Objectives:
1. Configure the appropriate hostnames and IP addresses on each device.  Enable router interfaces.
    (You don't have to configure ISPR1)

2. Configure a loopback interface on each router (1.1.1.1/32 for R1, 2.2.2.2/32 for R2, etc.)

3. Enable OSPF directly on each interface of the routers.
    Configure passive interfaces as appropriate.
 
4. Configure the reference bandwidth on each router so a FastEthernet interface
    has a cost of 100.

5. Configure R1 as an ASBR that advertises a default route in to the OSPF domain.

6. Check the routing tables of R4.  What default route(s) were added?
    (watch the video for a brief explanation)

7. Use Simulation mode to view the OSPF Hello messages being sent by the routers.
    What fields are included in the Hello message?
### 2. **Addressing Scheme**
| **Device** | **Interface** | **IP Address** | **Subnet Mask** | **Description** |
|------------|----------------|----------------|-----------------|-----------------|
| R1         | G0/0           | 10.0.12.1      | 255.255.255.252 | Link to R2      |
| R1         | F1/0           | 10.0.13.1      | 255.255.255.252 | Link to R3      |
| R1         | Loopback0      | 1.1.1.1        | 255.255.255.255 | Loopback        |
| R2         | G0/0           | 10.0.12.2      | 255.255.255.252 | Link to R1      |
| R2         | F1/0           | 10.0.24.1      | 255.255.255.252 | Link to R4      |
| R2         | Loopback0      | 2.2.2.2        | 255.255.255.255 | Loopback        |
| R3         | F1/0           | 10.0.13.2      | 255.255.255.252 | Link to R1      |
| R3         | F2/0           | 10.0.34.1      | 255.255.255.252 | Link to R4      |
| R3         | Loopback0      | 3.3.3.3        | 255.255.255.255 | Loopback        |
| R4         | F1/0           | 10.0.24.2      | 255.255.255.252 | Link to R2      |
| R4         | F2/0           | 10.0.34.2      | 255.255.255.252 | Link to R3      |
| R4         | G0/0           | 192.168.4.254  | 255.255.255.0   | Link to PC1     |
| R4         | Loopback0      | 4.4.4.4        | 255.255.255.255 | Loopback        |
| PC1        | NIC            | 192.168.4.1    | 255.255.255.0   |                 |

### 3. **Step-by-Step Configuration**
#### a. **Hostname and Interface Configuration**
Each router and switch must be configured with appropriate hostnames and IP addresses.

**R1:**
```bash
Router(config)# hostname R1
R1(config)# interface G0/0
R1(config-if)# ip address 10.0.12.1 255.255.255.252
R1(config-if)# no shutdown
R1(config)# interface F1/0
R1(config-if)# ip address 10.0.13.1 255.255.255.252
R1(config-if)# no shutdown
```

**R2:**
```bash
Router(config)# hostname R2
R2(config)# interface G0/0
R2(config-if)# ip address 10.0.12.2 255.255.255.252
R2(config-if)# no shutdown
R2(config)# interface F1/0
R2(config-if)# ip address 10.0.24.1 255.255.255.252
R2(config-if)# no shutdown
```

... Similar commands will be applied to R3, R4, and SW1.

#### b. **Loopback Interface Configuration**
Configure a loopback interface on each router for routing purposes.

**R1:**
```bash
R1(config)# interface loopback0
R1(config-if)# ip address 1.1.1.1 255.255.255.255
```
Repeat similar configurations for R2, R3, and R4, assigning `2.2.2.2/32`, `3.3.3.3/32`, and `4.4.4.4/32` respectively.

#### c. **OSPF Configuration**
Enable OSPF routing directly on each interface and configure passive interfaces where needed.

**R1:**
```bash
R1(config)# router ospf 1
R1(config-router)# network 10.0.12.0 0.0.0.3 area 0
R1(config-router)# network 10.0.13.0 0.0.0.3 area 0
R1(config-router)# network 1.1.1.1 0.0.0.0 area 0
R1(config-router)# passive-interface default
R1(config-router)# no passive-interface G0/0
R1(config-router)# no passive-interface F1/0
```
Repeat OSPF configuration for R2, R3, and R4, ensuring to include the correct network addresses for each router's interfaces.

#### d. **Reference Bandwidth**
Configure the reference bandwidth to adjust OSPF cost calculations, ensuring FastEthernet interfaces have a cost of 100.

```bash
R1(config)# router ospf 1
R1(config-router)# auto-cost reference-bandwidth 10000
```
Apply this configuration to all routers (R1, R2, R3, and R4).

#### e. **ASBR Configuration on R1**
R1 will be configured as an ASBR to advertise a default route into the OSPF domain.

```bash
R1(config)# ip route 0.0.0.0 0.0.0.0 G0/0
R1(config)# router ospf 1
R1(config-router)# default-information originate
```

#### f. **Routing Table Verification (R4)**
Check the routing table on R4 to see if the default route has been propagated by R1.

```bash
R4# show ip route
```
You should see the default route (`0.0.0.0/0`) in the routing table, which was learned from R1.

#### g. **OSPF Hello Messages in Simulation Mode**
To view OSPF Hello messages, switch to Simulation mode in Packet Tracer and inspect the packets exchanged between the routers.

Fields included in the Hello messages:
- Router ID
- Hello and Dead Intervals
- Neighbors
- Area ID
- Authentication Type (if configured)
- Network Mask

### 4. **Conclusion**
By following this guide, you will have configured OSPF on the network, enabling dynamic routing between all routers. R1 is configured as an ASBR, advertising a default route. The reference bandwidth is configured to adjust OSPF cost metrics for FastEthernet links, and OSPF Hello messages are observable in Simulation mode.

## Acknowledgements


Special thanks to **Jeremy's IT Lab** for providing valuable resources and tutorials that greatly contributed to the completion of this exercise. His in-depth explanations and practical demonstrations have been instrumental in enhancing my understanding of Cisco networking concepts and the effective use of Packet Tracer.

For more information and additional resources, visit [Jeremy's IT Lab](https://jeremysitlab.com/) and check out his YouTube for the full course, [Jeremy's IT Lab Free CCNA 200-301 | Complete Course](https://www.youtube.com/playlist?list=PLxbwE86jKRgMpuZuLBivzlM8s2Dk5lXBQ)
