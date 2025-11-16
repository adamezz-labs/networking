# DHCP Relay Network Topology

## Overview
This repository contains the configuration and documentation for a two-router network topology implementing DHCP services with relay functionality.

## Network Diagram

```
[PC1] -------- [R1] -------- [R2] -------- [PC2]
               /  \                  \
              /    \                  \
           [PC3]  [PC4]              [PC3]
```

## Network Architecture

### Topology Components
- **Router R1**: Primary DHCP server and gateway
- **Router R2**: DHCP relay agent
- **Multiple client networks**: Connected to both routers

### IP Addressing Scheme

| Device | Interface | IP Address | Subnet Mask | Network |
|--------|-----------|------------|-------------|---------|
| R1 | G0/0 | 192.168.1.65 | 255.255.255.224 | 192.168.1.64/27 |
| R1 | G0/1 | 10.0.0.1 | 255.255.255.252 | 10.0.0.0/30 |
| R1 | G0/2 | 192.168.1.1 | 255.255.255.192 | 192.168.1.0/26 |
| R2 | G0/0 | 10.0.0.2 | 255.255.255.252 | 10.0.0.0/30 |
| R2 | G0/1 | 192.168.1.97 | 255.255.255.240 | 192.168.1.96/28 |

## DHCP Configuration

### DHCP Pools on R1

#### R1_Client_LAN Pool
- **Network**: 192.168.1.0/26
- **Available IPs**: 192.168.1.1 - 192.168.1.62
- **Excluded Range**: 192.168.1.1 - 192.168.1.5
- **Default Gateway**: 192.168.1.1
- **Domain Name**: ccna-lab.com
- **Usable Addresses**: 60

#### R2_Client_LAN Pool
- **Network**: 192.168.1.96/28
- **Available IPs**: 192.168.1.97 - 192.168.1.110
- **Excluded Range**: 192.168.1.97 - 192.168.1.101
- **Default Gateway**: 192.168.1.97
- **Domain Name**: ccna-lab.com
- **Usable Addresses**: 12

### DHCP Relay Configuration
- **R2 Interface G0/1** configured with IP helper-address pointing to **10.0.0.1** (R1)
- Forwards DHCP requests from clients on 192.168.1.96/28 network to R1

## Routing Configuration

Both routers are configured with default static routes:
- **R1**: Default route via 10.0.0.2 (R2)
- **R2**: Default route via 10.0.0.1 (R1)

## Features

- ✅ Centralized DHCP server on R1
- ✅ DHCP relay functionality on R2
- ✅ Multiple subnet support
- ✅ Static routing between routers
- ✅ Reserved IP addresses for network devices

## Files in This Repository

- `README.md` - This file
- `R1config.txt` - Complete configuration for Router 1
- `R2.txt` - Complete configuration for Router 2
- `CONFIGURATION.md` - Detailed configuration steps
- `TROUBLESHOOTING.md` - Common issues and solutions

## Equipment

- **Router Model**: Cisco 2911/K9
- **IOS Version**: 15.1(4)M5
- **Memory**: 512 MB
- **Interfaces**: 3x Gigabit Ethernet per router

## Getting Started

1. Review the configuration files for each router
2. Apply configurations in the order: R1 first, then R2
3. Verify DHCP pool status with `show ip dhcp pool`
4. Test DHCP relay with client devices on R2's network

## License

This configuration is provided for educational purposes.

## Author

Network Lab Configuration - CCNA Study Material
