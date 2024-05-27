# Network Design and Configuration Plan

## Network Overview

This network design includes multiple VLANs across several switches, including Core Switches, Distribution Switches, and Access Switches.
The design incorporates dynamic routing with OSPF, VLAN segmentation, and DHCP for IP address management.
Spanning Tree Protocol (STP) is used to ensure a loop-free topology.

## Network Topology Diagram

    CoreSwitch1 --> DistSwitch1;
    CoreSwitch1 --> DistSwitch2;
    DistSwitch1 --> AccessSwitch1;
    DistSwitch1 --> AccessSwitch2;
    DistSwitch2 --> AccessSwitch3;
    DistSwitch2 --> AccessSwitch4;
    AccessSwitch1 --> ESwitch5;
    AccessSwitch1 --> ESwitch6;
    AccessSwitch2 --> ESwitch7;
    AccessSwitch2 --> ESwitch8;
    AccessSwitch3 --> ESwitch9;
    AccessSwitch3 --> ESwitch10;
    AccessSwitch4 --> ESwitch11;
    AccessSwitch4 --> ESwitch12;

## VLAN Configuration

### VLANs

| VLAN ID | Name    | Subnet          |
| ------- | ------- | --------------- |
| 99      | Mgmt    | 192.168.99.0/24 |
| 100     | VLAN100 | 192.168.1.0/24  |
| 101     | VLAN101 | 192.168.2.0/24  |
| 102     | VLAN102 | 192.168.3.0/24  |
| 200     | VLAN200 | 172.16.1.0/24   |
| 201     | VLAN201 | 172.16.2.0/24   |
| 202     | VLAN202 | 172.16.3.0/24   |
| 203     | VLAN203 | 172.16.4.0/24   |
| 204     | VLAN204 | 172.16.5.0/24   |
| 205     | VLAN205 | 172.16.6.0/24   |

## IP Address Configuration

### Core Switch

```shell
conf t
interface Vlan99
 ip address 192.168.99.1 255.255.255.0
 no shutdown
!
interface Vlan100
 ip address 192.168.1.1 255.255.255.0
 no shutdown
!
interface Vlan101
 ip address 192.168.2.1 255.255.255.0
 no shutdown
!
interface Vlan102
 ip address 192.168.3.1 255.255.255.0
 no shutdown
!
interface Vlan200
 ip address 172.16.1.1 255.255.255.0
 no shutdown
!
interface Vlan201
 ip address 172.16.2.1 255.255.255.0
 no shutdown
!
interface Vlan202
 ip address 172.16.3.1 255.255.255.0
 no shutdown
!
interface Vlan203
 ip address 172.16.4.1 255.255.255.0
 no shutdown
!
interface Vlan204
 ip address 172.16.5.1 255.255.255.0
 no shutdown
!
interface Vlan205
 ip address 172.16.6.1 255.255.255.0
 no shutdown
!
router ospf 1
 network 192.168.99.0 0.0.0.255 area 0
 network 192.168.1.0 0.0.0.255 area 0
 network 192.168.2.0 0.0.0.255 area 0
 network 192.168.3.0 0.0.0.255 area 0
 network 172.16.1.0 0.0.0.255 area 0
 network 172.16.2.0 0.0.0.255 area 0
 network 172.16.3.0 0.0.0.255 area 0
 network 172.16.4.0 0.0.0.255 area 0
 network 172.16.5.0 0.0.0.255 area 0
 network 172.16.6.0 0.0.0.255 area 0
exit
```

### Distribution Switches

```shell
conf t
interface Vlan99
 ip address 192.168.99.2 255.255.255.0
 no shutdown
!
router ospf 1
 network 192.168.99.0 0.0.0.255 area 0
exit
```

### Access Switches

```shell
conf t
interface Vlan99
 ip address 192.168.99.3 255.255.255.0
 no shutdown
!
interface Vlan200
 no ip address
!
interface Vlan201
 no ip address
!
interface Vlan202
 no ip address
!
interface Vlan203
 no ip address
!
interface Vlan204
 no ip address
!
interface Vlan205
 no ip address
!
interface GigabitEthernet0/1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 99-102,200-205
 switchport mode trunk
!
interface GigabitEthernet0/2
 switchport trunk native vlan 99
 switchport trunk allowed vlan 99-102,200-205
 switchport mode trunk
!
exit
```

## DHCP Configuration

### DHCP Server Configuration on CoreSwitch1

```shell
conf t
ip dhcp pool VLAN100
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 192.168.99.1
!
ip dhcp pool VLAN101
 network 192.168.2.0 255.255.255.0
 default-router 192.168.2.1
 dns-server 192.168.99.1
!
ip dhcp pool VLAN102
 network 192.168.3.0 255.255.255.0
 default-router 192.168.3.1
 dns-server 192.168.99.1
!
ip dhcp pool VLAN200
 network 172.16.1.0 255.255.255.0
 default-router 172.16.1.1
 dns-server 192.168.99.1
!
ip dhcp pool VLAN201
 network 172.16.2.0 255.255.255.0
 default-router 172.16.2.1
 dns-server 192.168.99.1
!
ip dhcp pool VLAN202
 network 172.16.3.0 255.255.255.0
 default-router 172.16.3.1
 dns-server 192.168.99.1
!
ip dhcp pool VLAN203
 network 172.16.4.0 255.255.255.0
 default-router 172.16.4.1
 dns-server 192.168.99.1
!
ip dhcp pool VLAN204
 network 172.16.5.0 255.255.255.0
 default-router 172.16.5.1
 dns-server 192.168.99.1
!
ip dhcp pool VLAN205
 network 172.16.6.0 255.255.255.0
 default-router 172.16.6.1
 dns-server 192.168.99.1
!
```

## Spanning Tree Protocol (STP)

### STP Configuration

```shell
conf t
spanning-tree mode pvst
spanning-tree vlan 1 priority 4096
spanning-tree vlan 99 priority 8192
exit
```

### STP Verification

```shell
show spanning-tree
```

## Summary

- **CoreSwitch1**: Central point of routing for all VLANs with DHCP and OSPF configured.
- **Distribution Switches**: Serve as intermediaries between Core and Access switches, with OSPF enabled.
- **Access Switches**: Provide connectivity to end devices, configured with management VLANs and trunk ports for VLAN propagation.
- **E-Switches**: Endpoint switches configured for VLANs and access ports.
- **DHCP**: Configured on CoreSwitch1 to provide IP addresses to devices on different VLANs.
- **STP**: Enabled on all switches to prevent loops, with priorities set for VLANs.

 `Network_Design.md`