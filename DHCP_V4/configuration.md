# Configuration Guide

## Router 1 (R1) Configuration

### Basic Configuration

```cisco
enable
configure terminal
hostname R1
```

### Interface Configuration

#### G0/0 - Client Network 1
```cisco
interface GigabitEthernet0/0
ip address 192.168.1.65 255.255.255.224
no shutdown
exit
```

#### G0/1 - WAN Link to R2
```cisco
interface GigabitEthernet0/1
ip address 10.0.0.1 255.255.255.252
no shutdown
exit
```

#### G0/2 - Client Network 2
```cisco
interface GigabitEthernet0/2
ip address 192.168.1.1 255.255.255.192
no shutdown
exit
```

### Static Routing
```cisco
ip route 0.0.0.0 0.0.0.0 10.0.0.2
```

### DHCP Server Configuration

#### Exclude Addresses
```cisco
ip dhcp excluded-address 192.168.1.1 192.168.1.5
ip dhcp excluded-address 192.168.1.97 192.168.1.101
```

#### DHCP Pool for R1 Clients
```cisco
ip dhcp pool R1_Client_LAN
network 192.168.1.0 255.255.255.192
default-router 192.168.1.1
domain-name ccna-lab.com
exit
```

#### DHCP Pool for R2 Clients
```cisco
ip dhcp pool R2_Client_LAN
network 192.168.1.96 255.255.255.240
default-router 192.168.1.97
domain-name ccna-lab.com
exit
```

### Save Configuration
```cisco
do copy running-config startup-config
```

---

## Router 2 (R2) Configuration

### Basic Configuration

```cisco
enable
configure terminal
hostname R2
```

### Interface Configuration

#### G0/0 - WAN Link to R1
```cisco
interface GigabitEthernet0/0
ip address 10.0.0.2 255.255.255.252
no shutdown
exit
```

#### G0/1 - Client Network with DHCP Relay
```cisco
interface GigabitEthernet0/1
ip address 192.168.1.97 255.255.255.240
no shutdown
ip helper-address 10.0.0.1
exit
```

### Static Routing
```cisco
ip route 0.0.0.0 0.0.0.0 10.0.0.1
```

### Save Configuration
```cisco
do copy running-config startup-config
```

---

## Verification Commands

### DHCP Server Verification (R1)
```cisco
show ip dhcp pool
show ip dhcp binding
show ip dhcp server statistics
```

### DHCP Relay Verification (R2)
```cisco
show ip interface GigabitEthernet0/1
show running-config interface GigabitEthernet0/1
```

### Connectivity Tests
```cisco
ping 10.0.0.1
ping 10.0.0.2
ping 192.168.1.1
ping 192.168.1.97
```

### Routing Verification
```cisco
show ip route
show ip interface brief
```

---

## Configuration Notes

### IP Helper-Address
- Configured on R2's G0/1 interface
- Forwards DHCP broadcast packets to R1 (10.0.0.1)
- Enables centralized DHCP management

### DHCP Excluded Addresses
- **192.168.1.1 - 192.168.1.5**: Reserved for R1 and network devices
- **192.168.1.97 - 192.168.1.101**: Reserved for R2 and network devices

### Subnet Breakdown

**192.168.1.0/26 (R1 Local Network)**
- Network: 192.168.1.0
- First Host: 192.168.1.1 (R1 Gateway)
- Last Host: 192.168.1.62
- Broadcast: 192.168.1.63
- Total Hosts: 62

**192.168.1.96/28 (R2 Remote Network)**
- Network: 192.168.1.96
- First Host: 192.168.1.97 (R2 Gateway)
- Last Host: 192.168.1.110
- Broadcast: 192.168.1.111
- Total Hosts: 14

**10.0.0.0/30 (WAN Link)**
- Network: 10.0.0.0
- R1: 10.0.0.1
- R2: 10.0.0.2
- Broadcast: 10.0.0.3
- Total Hosts: 2

---

## Common Issues During Configuration

### Issue: "Invalid input detected" when saving
**Solution**: Exit configuration mode first or use `do` prefix:
```cisco
do copy running-config startup-config
```

### Issue: DHCP clients not receiving addresses on R2 network
**Solution**: Verify IP helper-address is configured on R2's G0/1 interface

### Issue: No connectivity between routers
**Solution**: Check that both ends of WAN link are configured and no shutdown

---

## Best Practices

1. Always save configuration after making changes
2. Verify interface status with `show ip interface brief`
3. Test DHCP functionality on both networks
4. Document any custom configurations
5. Keep backup copies of working configurations
