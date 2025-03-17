# Static Routing in a Multi-Router Topology

## Overview

This GNS3 project demonstrates the implementation of static routing in a network consisting of four routers (R1 to R4) and two end devices (PC1 and PC2). The goal is to configure static routes to enable communication between PC1 and PC2 through the path R1 → R3 → R4.

## Topology

The network setup consists of:
- **Routers**: R1, R2, R3, R4 (Cisco c7200 routers)
- **PCs**: PC1 (connected to R1) and PC2 (connected to R4)

### IP Addressing Scheme

| Connection | Network Address |
|------------|-----------------|
| R1 ↔ R2 | 192.168.12.0/24 |
| R1 ↔ R3 | 192.168.13.0/24 |
| R2 ↔ R4 | 192.168.24.0/24 |
| R3 ↔ R4 | 192.168.34.0/24 |
| PC1 ↔ R1 | 192.168.1.0/24 (PC1: 192.168.1.10, Default Gateway: 192.168.1.1) |
| PC2 ↔ R4 | 192.168.4.0/24 (PC2: 192.168.4.10, Default Gateway: 192.168.4.1) |

## Network Diagram

```
   PC1
    |
    |
    R1 ---------- R2
    |              |
    |              |
    R3 ---------- R4
                   |
                   |
                  PC2
```

## Objective

Enable PC1 to successfully ping PC2 by manually configuring static routes on all routers.

## Configuration Steps

### 1. IP Address Configuration

Configure the IP addresses on all interfaces of the routers and PCs based on the addressing scheme above.

### 2. Enable IP Routing

Enable IP routing on all routers using:

```
R(config)# ip routing
```

### 3. Configure Static Routes

Manually define static routes on each router:

**R1**:
```
ip route 192.168.4.0 255.255.255.0 192.168.13.3
```

**R2**:
```
ip route 192.168.1.0 255.255.255.0 GigabitEthernet0/0 192.168.12.1
ip route 192.168.4.0 255.255.255.0 GigabitEthernet1/0 192.168.24.4
```

**R3**:
```
ip route 192.168.1.0 255.255.255.0 192.168.13.1
ip route 192.168.4.0 255.255.255.0 192.168.34.4
```

**R4**:
```
ip route 192.168.1.0 255.255.255.0 192.168.34.3

```

### 4. Test Connectivity

- Verify connectivity between directly connected networks using ping.
- Test end-to-end communication by pinging PC2 (192.168.4.10) from PC1 (192.168.1.10).

## Complete Router Configurations

<details>
<summary>Router R1 Configuration</summary>

```
hostname R1
!
ip routing
!
interface G2/0
 description Connection to PC1
 ip address 192.168.1.1 255.255.255.0
 no shutdown
!
interface G1/0
 description Connection to R2
 ip address 192.168.12.1 255.255.255.0
 no shutdown
!
interface G0/0
 description Connection to R3
 ip address 192.168.13.1 255.255.255.0
 no shutdown
!
ip route 192.168.4.0 255.255.255.0 192.168.13.3
!
end
```
</details>

<details>
<summary>Router R2 Configuration</summary>

```
hostname R2
!
ip routing
!
interface G0/0
 description Connection to R1
 ip address 192.168.12.2 255.255.255.0
 no shutdown
!
interface G1/0
 description Connection to R4
 ip address 192.168.24.2 255.255.255.0
 no shutdown
!
ip route 192.168.1.0 255.255.255.0 GigabitEthernet0/0 192.168.12.1
ip route 192.168.4.0 255.255.255.0 GigabitEthernet1/0 192.168.24.4
!
end
```
</details>

<details>
<summary>Router R3 Configuration</summary>

```
hostname R3
!
ip routing
!
interface G0/0
 description Connection to R1
 ip address 192.168.13.3 255.255.255.0
 no shutdown
!
interface G1/0
 description Connection to R4
 ip address 192.168.34.3 255.255.255.0
 no shutdown
!
ip route 192.168.1.0 255.255.255.0 192.168.13.1
ip route 192.168.4.0 255.255.255.0 192.168.34.4
!
end
```
</details>

<details>
<summary>Router R4 Configuration</summary>

```
hostname R4
!
ip routing
!
interface G0/0
 description Connection to R2
 ip address 192.168.24.4 255.255.255.0
 no shutdown
!
interface G1/0
 description Connection to R3
 ip address 192.168.34.4 255.255.255.0
 no shutdown
!
interface FastEthernet2/0
 description Connection to PC2
 ip address 192.168.4.1 255.255.255.0
 no shutdown
!
ip route 192.168.1.0 255.255.255.0 192.168.34.3
!
end
```
</details>

## Tools Used

- **GNS3**: Network simulation platform
- **Wireshark**: Packet analysis tool for troubleshooting
- **Cisco c7200 Routers**: Used for routing configuration

## Key Takeaways

- Static routing requires manually defining routes for each destination network
- It is straightforward but not scalable for larger networks
- Proper planning of IP addressing and routing ensures successful packet delivery

## How to Use the Project

1. Clone this repository or download the project files
2. Import the project into GNS3
3. Ensure you have the required Cisco c7200 router images in your GNS3 setup
4. Apply the provided configurations to each device
5. Test connectivity using the ping command from PC1 to PC2

## Verification Commands

Use these commands to verify your configuration:
```
show ip route
show ip interface brief
ping 192.168.4.10  # From PC1 to verify end-to-end connectivity
traceroute 192.168.4.10  # To see the path packets take
```

## Requirements

- Cisco c7200 router images
- Sufficient system resources to run 4 virtual routers

## Notes

- Reset MAC addresses when importing the project to avoid conflicts
- Include your own router base images as per licensing requirements
- The preferred path for traffic is R1 → R3 → R4, but the topology also supports R1 → R2 → R4
