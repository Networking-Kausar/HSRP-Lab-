# HSRP-Lab-
# CCNA Lab - HSRP (Hot Standby Router Protocol) Gateway Redundancy

## Overview

This lab demonstrates the implementation of HSRP (Hot Standby Router Protocol) using two Cisco 1941 routers and a Cisco 2960 switch in Packet Tracer.

HSRP provides gateway redundancy by allowing multiple routers to share a single virtual default gateway. If the active router fails, the standby router automatically takes over, ensuring continuous network availability.

---

## Lab Objectives

* Configure HSRP on Cisco 1941 routers
* Understand Active and Standby router roles
* Configure a Virtual IP (VIP)
* Implement Priority and Preemption
* Verify HSRP operation
* Test failover and recovery
* Understand First Hop Redundancy Protocols (FHRP)

---

## Network Topology

```text
                    Virtual Gateway
                      192.168.10.1


        192.168.10.2                 192.168.10.3
          +------+                     +------+
          |  R1  |                     |  R2  |
          | 1941 |                     | 1941 |
          +------+                     +------+
               \                         /
                \                       /
                 \                     /
                  \                   /
                   +-----------------+
                   |      SW1        |
                   | Cisco 2960-24TT |
                   +-----------------+
                           |
                           |
                         PC1
                    192.168.10.10
```

---

## Device Requirements

* 2 × Cisco 1941 Routers
* 1 × Cisco 2960 Switch
* 1 × PC
* Copper Straight-Through Cables

---

## Physical Connections

| Device | Interface     | Connected To | Interface |
| ------ | ------------- | ------------ | --------- |
| R1     | G0/0          | SW1          | F0/1      |
| R2     | G0/0          | SW1          | F0/2      |
| PC1    | FastEthernet0 | SW1          | F0/3      |

### Cable Type

```text
Copper Straight-Through
```

---

## IP Addressing Table

| Device          | Interface     | IP Address    | Subnet Mask   |
| --------------- | ------------- | ------------- | ------------- |
| R1              | G0/0          | 192.168.10.2  | 255.255.255.0 |
| R2              | G0/0          | 192.168.10.3  | 255.255.255.0 |
| PC1             | FastEthernet0 | 192.168.10.10 | 255.255.255.0 |
| HSRP Virtual IP | Group 1       | 192.168.10.1  | 255.255.255.0 |

---

## Router R1 Configuration

```bash
enable
configure terminal
hostname R1

interface g0/0
 ip address 192.168.10.2 255.255.255.0
 no shutdown

 standby 1 ip 192.168.10.1
 standby 1 priority 120
 standby 1 preempt

end
write memory
```

### R1 Configuration Notes

* Virtual IP: 192.168.10.1
* Priority: 120
* Role: Active Router
* Preemption Enabled

---

## Router R2 Configuration

```bash
enable
configure terminal
hostname R2

interface g0/0
 ip address 192.168.10.3 255.255.255.0
 no shutdown

 standby 1 ip 192.168.10.1
 standby 1 priority 100
 standby 1 preempt

end
write memory
```

### R2 Configuration Notes

* Virtual IP: 192.168.10.1
* Priority: 100
* Role: Standby Router
* Preemption Enabled

---

## PC Configuration

Desktop → IP Configuration

```text
IP Address:      192.168.10.10
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.10.1
```

---

## How HSRP Works

### Normal Operation

```text
PC1
 |
 v
192.168.10.1 (Virtual Gateway)
 |
 v
R1 (Active Router)

R2 (Standby Router)
```

Traffic is forwarded through R1 because it has the highest priority.

---

### Failover Operation

If R1 becomes unavailable:

```text
PC1
 |
 v
192.168.10.1 (Virtual Gateway)
 |
 v
R2 (Active Router)
```

R2 automatically assumes the Active role without requiring any changes on the PC.

---

## Verification Commands

### Verify HSRP Status

```bash
show standby brief
```

Expected Results:

### R1

```text
Interface   Grp  Pri  State
Gi0/0       1    120  Active
```

### R2

```text
Interface   Grp  Pri  State
Gi0/0       1    100  Standby
```

---

### Verify Interfaces

```bash
show ip interface brief
```

---

### Detailed HSRP Information

```bash
show standby
```

---

## Connectivity Testing

From PC1 Command Prompt:

```bash
ping 192.168.10.1
ping 192.168.10.2
ping 192.168.10.3
```

All pings should be successful.

---

## Failover Testing

### Simulate Failure on R1

```bash
configure terminal
interface g0/0
shutdown
```

### Verify R2 Becomes Active

```bash
show standby brief
```

Expected:

```text
State: Active
```

---

## Recovery Testing

Bring R1 back online:

```bash
configure terminal
interface g0/0
no shutdown
```

Since R1 has the highest priority and preemption enabled, it will automatically reclaim the Active role.

---

## Key Concepts Learned

### HSRP

Hot Standby Router Protocol provides gateway redundancy for hosts.

### Virtual IP

Shared gateway address used by end devices.

### Active Router

The router currently forwarding traffic.

### Standby Router

Backup router waiting to take over.

### Priority

Determines which router becomes Active.

### Preemption

Allows a higher-priority router to regain Active status after recovery.

---

## Skills Demonstrated

* Cisco IOS Configuration
* HSRP Deployment
* Gateway Redundancy
* Network Resiliency
* Packet Tracer Simulation
* Troubleshooting and Verification
* First Hop Redundancy Protocols (FHRP)

---

## Author

**Muhammad Kausar**

CCNA Networking Lab Series

This project demonstrates the implementation of HSRP using Cisco Packet Tracer to provide gateway redundancy and improve network availability in an enterprise environment.

