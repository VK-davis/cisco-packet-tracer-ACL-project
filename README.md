# Cisco Packet Tracer ACL Project

## Project Overview

This project demonstrates the implementation of Access Control Lists (ACLs) on a Cisco router to control and filter network traffic. The network topology was created using Cisco Packet Tracer, and various ACLs were applied to secure the network by permitting or denying specific traffic.

### Key Features:
- **Inter-VLAN Routing**: The router performs inter-VLAN routing, allowing communication between different VLANs while applying ACLs for traffic filtering.
- **ACL Implementation**: ACLs were applied to restrict ICMP traffic, permit HTTP traffic, and block certain traffic from untrusted sources.
- **Routing Protocol**: RIP version 2 was configured to enable routing between different networks in the topology.

## Network Topology

The network topology consists of multiple VLANs connected to a router with sub-interfaces configured for each VLAN. An ACL was applied to each sub-interface to control the traffic allowed into the network.

## ACL Configuration

### GigabitEthernet Interfaces
- **GigabitEthernet0/0/0**: 
  - **Sub-interface 101**: Configured with VLAN 101 and assigned IP address `10.10.1.1`. The ACL `BLOCK_ALL_EXCEPT_TCP_HTTP` was applied to permit only HTTP traffic.
  - **Sub-interface 102**: Configured with VLAN 102 and assigned IP address `10.10.2.1`. The ACL `BLOCK_ALL_EXCEPT_TCP_HTTP` was applied to permit only HTTP traffic.
  - **Sub-interface 103**: Configured with VLAN 103 and assigned IP address `10.10.3.1`. The ACL `101` was applied to block ICMP traffic between specific hosts and permit all other traffic.
- **GigabitEthernet0/0/1**: 
  - Configured with IP address `12.12.1.1`. The ACL `UNTRUSTED_SOURCE` was applied to deny traffic from an untrusted source and permit all other traffic.

### Access Control Lists (ACLs)
- **ACL 101**: 
  - Denies ICMP traffic from host `10.10.3.2` to host `10.10.1.3`.
  - Permits all other IP traffic.
- **BLOCK_ALL_EXCEPT_TCP_HTTP**:
  - Permits only HTTP (TCP port 80) traffic to the network `11.11.1.0/30`.
- **UNTRUSTED_SOURCE**:
  - Denies traffic from untrusted host `11.11.1.2` to host `10.10.3.2`.
  - Permits all other IP traffic.

## Access Control and Security

### Line Console 0
- **Password**: @DmIn852456
- **Login**: Enabled

### Line Auxiliary 0
- **Password**: @DmIn852456
- **Login**: Enabled

### Line VTY 0 4
- **Access Class**: 10 (inbound)
- **Password**: @DmIn852456
- **Login**: Enabled
- **Transport Input**: Telnet

## Logging Configuration

### Packet Tracer Limitations
- **Logging Buffered**: Packet Tracer supports logging buffered but with limited options.
- **Logging Console**: Supported for basic console logging.
- **Logging Monitor**: May not be fully supported in Packet Tracer. In real environments, it is used to log messages to remote sessions.

## RIP Authentication (Not Implemented in Packet Tracer)

Packet Tracer does not support RIP authentication. In a real Cisco environment or more advanced simulation tools like GNS3 or Cisco VIRL, RIP authentication can be configured to enhance routing security.

### Example Configuration for RIP Authentication:

```plaintext
key chain RIP_KEY
 key 1
  key-string yourkey

interface GigabitEthernet0/0/0.101
 ip rip authentication mode md5
 ip rip authentication key-chain RIP_KEY

interface GigabitEthernet0/0/0.102
 ip rip authentication mode md5
 ip rip authentication key-chain RIP_KEY

interface GigabitEthernet0/0/0.103
 ip rip authentication mode md5
 ip rip authentication key-chain RIP_KEY

interface GigabitEthernet0/0/1
 ip rip authentication mode md5
 ip rip authentication key-chain RIP_KEY
```
## Configuration Script

The running configuration of the router is provided below:

```plaintext
version 16.6.4
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
hostname Router
ip cef
no ipv6 cef
spanning-tree mode pvst
interface GigabitEthernet0/0/0
 no ip address
 ip access-group BLOCK_ALL_EXCEPT_TCP_HTTP in
 duplex auto
 speed auto
interface GigabitEthernet0/0/0.101
 encapsulation dot1Q 101
 ip address 10.10.1.1 255.255.255.0
 ip access-group BLOCK_ALL_EXCEPT_TCP_HTTP in
interface GigabitEthernet0/0/0.102
 encapsulation dot1Q 102
 ip address 10.10.2.1 255.255.255.0
 ip access-group BLOCK_ALL_EXCEPT_TCP_HTTP in
interface GigabitEthernet0/0/0.103
 encapsulation dot1Q 103
 ip address 10.10.3.1 255.255.255.0
 ip access-group 101 in
interface GigabitEthernet0/0/1
 ip address 12.12.1.1 255.255.255.252
 ip access-group UNTRUSTED_SOURCE in
 duplex auto
 speed auto
interface GigabitEthernet0/0/2
 no ip address
 duplex auto
 speed auto
 shutdown
router rip
 version 2
 network 10.0.0.0
 network 12.0.0.0
ip classless
ip flow-export version 9
access-list 101 deny icmp host 10.10.3.2 host 10.10.1.3
access-list 101 permit ip any any
ip access-list extended BLOCK_ALL_EXCEPT_TCP_HTTP
 permit tcp any 11.11.1.0 0.0.0.3 eq www
ip access-list extended UNTRUSTED_SOURCE
 deny ip host 11.11.1.2 host 10.10.3.2
 permit ip any any
line con 0
line aux 0
line vty 0 4
 login

```


## Additional Notes

- **Packet Tracer Limitations**: Some advanced features such as `crypto key generate rsa` and full RIP authentication support are not available in Packet Tracer. Use GNS3 or Cisco VIRL for a complete feature set.
