# Troubleshooting Guide

## DHCP Issues

### Problem: Clients on R2 network not receiving IP addresses

#### Symptoms
- Clients connected to R2's G0/1 show APIPA addresses (169.254.x.x)
- DHCP requests timing out
- No IP address assigned

#### Diagnostic Steps

1. **Verify IP Helper-Address on R2**
   ```cisco
   R2# show running-config interface GigabitEthernet0/1
   ```
   Should display: `ip helper-address 10.0.0.1`

2. **Check DHCP Pool on R1**
   ```cisco
   R1# show ip dhcp pool R2_Client_LAN
   ```
   Verify pool exists and has available addresses

3. **Test Connectivity Between Routers**
   ```cisco
   R2# ping 10.0.0.1
   ```
   Must be successful for DHCP relay to work

4. **Check DHCP Bindings on R1**
   ```cisco
   R1# show ip dhcp binding
   ```
   Look for leases from the 192.168.1.96/28 network

#### Solutions

**If IP helper-address is missing:**
```cisco
R2(config)# interface GigabitEthernet0/1
R2(config-if)# ip helper-address 10.0.0.1
```

**If DHCP pool is misconfigured:**
```cisco
R1(config)# ip dhcp pool R2_Client_LAN
R1(dhcp-config)# network 192.168.1.96 255.255.255.240
R1(dhcp-config)# default-router 192.168.1.97
```

**If connectivity is broken:**
- Check interface status: `show ip interface brief`
- Verify routing: `show ip route`

---

### Problem: DHCP pool exhausted

#### Symptoms
- Message: "No more addresses available in pool"
- New clients cannot obtain addresses

#### Diagnostic Steps
```cisco
R1# show ip dhcp pool
```
Check "Leased addresses" vs "Total addresses"

#### Solutions

1. **Clear old bindings:**
   ```cisco
   R1# clear ip dhcp binding *
   ```

2. **Adjust excluded addresses to free up more IPs:**
   ```cisco
   R1(config)# no ip dhcp excluded-address 192.168.1.1 192.168.1.5
   R1(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.3
   ```

3. **Increase subnet size** (requires network redesign)

---

## Connectivity Issues

### Problem: No connectivity between R1 and R2

#### Symptoms
- Ping fails between 10.0.0.1 and 10.0.0.2
- DHCP relay not working
- Clients on opposite networks cannot communicate

#### Diagnostic Steps

1. **Check interface status:**
   ```cisco
   R1# show ip interface brief
   R2# show ip interface brief
   ```
   Both G0/1 (R1) and G0/0 (R2) must show "up/up"

2. **Verify IP addresses:**
   ```cisco
   R1# show running-config interface GigabitEthernet0/1
   R2# show running-config interface GigabitEthernet0/0
   ```

3. **Check physical connectivity:**
   - Cable properly connected
   - Link lights active on both ends

#### Solutions

**If interface is administratively down:**
```cisco
R1(config)# interface GigabitEthernet0/1
R1(config-if)# no shutdown
```

**If IP address is incorrect:**
```cisco
R1(config)# interface GigabitEthernet0/1
R1(config-if)# ip address 10.0.0.1 255.255.255.252
```

**If line protocol is down:**
- Check physical cable
- Try different cable
- Check for speed/duplex mismatch

---

### Problem: Clients cannot reach other networks

#### Symptoms
- Local network works fine
- Cannot ping devices on other subnets
- Internet access fails

#### Diagnostic Steps

1. **Check default route:**
   ```cisco
   R1# show ip route
   R2# show ip route
   ```
   Look for `S* 0.0.0.0/0` entry

2. **Verify gateway configuration in DHCP:**
   ```cisco
   R1# show ip dhcp pool
   ```
   Check default-router setting

3. **Test routing from client:**
   ```cmd
   tracert 192.168.1.97
   ```

#### Solutions

**If default route is missing:**
```cisco
R1(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.2
R2(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.1
```

**If DHCP not providing gateway:**
```cisco
R1(config)# ip dhcp pool R1_Client_LAN
R1(dhcp-config)# default-router 192.168.1.1
```

---

## Configuration Issues

### Problem: Cannot save configuration

#### Symptoms
- Error: "Invalid input detected at '^' marker"
- Configuration lost after reload

#### Solutions

**Use the correct command:**
```cisco
# From privileged EXEC mode:
R1# copy running-config startup-config

# From configuration mode:
R1(config)# do copy running-config startup-config
```

**If flash memory is full:**
```cisco
R1# show flash:
R1# delete flash:filename
R1# squeeze flash:
```

---

### Problem: Interface configuration not applying

#### Symptoms
- Commands appear to work but show running-config doesn't reflect changes
- Interface doesn't come up

#### Diagnostic Steps
```cisco
R1# show running-config interface GigabitEthernet0/1
R1# show ip interface GigabitEthernet0/1
```

#### Solutions

1. **Exit and re-enter interface configuration:**
   ```cisco
   R1(config-if)# exit
   R1(config)# interface GigabitEthernet0/1
   ```

2. **Clear interface configuration:**
   ```cisco
   R1(config)# default interface GigabitEthernet0/1
   ```
   Then reconfigure from scratch

---

## Performance Issues

### Problem: Slow DHCP address assignment

#### Symptoms
- Long delay before client receives IP
- Multiple DHCP discover messages

#### Diagnostic Steps
```cisco
R1# debug ip dhcp server events
R1# debug ip dhcp server packet
```
(Remember to `undebug all` when finished)

#### Solutions

1. **Reduce excluded address range:**
   Fewer excluded addresses means faster pool scanning

2. **Check network congestion:**
   - High broadcast traffic
   - Network loops (check for redundant connections)

3. **Verify DHCP server isn't overloaded:**
   ```cisco
   R1# show ip dhcp server statistics
   ```

---

## Verification Command Reference

### Quick Health Check
```cisco
show ip interface brief          # Interface status
show ip route                    # Routing table
show ip dhcp pool                # DHCP pool status
show ip dhcp binding             # Active DHCP leases
ping 10.0.0.1                   # Test WAN connectivity
ping 192.168.1.1                # Test local gateway
```

### Detailed Diagnostics
```cisco
show running-config              # Current configuration
show startup-config              # Saved configuration
show ip protocols                # Routing protocols
show interfaces GigabitEthernet0/1 # Detailed interface info
show ip dhcp conflict            # DHCP address conflicts
show ip dhcp server statistics   # DHCP server performance
```

---

## Common Error Messages

### "Invalid input detected at '^' marker"
- **Cause**: Command syntax error or command not available in current mode
- **Solution**: Check command spelling, verify you're in correct mode, use `?` for help

### "Incomplete command"
- **Cause**: Required parameter missing
- **Solution**: Use `?` to see required parameters

### "Ambiguous command"
- **Cause**: Command abbreviation matches multiple commands
- **Solution**: Type more characters or use full command

### "%DHCP-4-PING_CONFLICT"
- **Cause**: IP address conflict detected
- **Solution**: Clear binding: `clear ip dhcp binding [address]`

---

## Best Practices for Troubleshooting

1. **Start with physical layer** - cables, lights, interface status
2. **Work up the OSI model** - physical → data link → network → transport
3. **Verify configuration** before assuming hardware failure
4. **Use systematic approach** - don't make random changes
5. **Document changes** - keep notes on what you tried
6. **Test after each change** - verify if issue is resolved
7. **Save working configurations** - have rollback option
8. **Use debug carefully** - can impact router performance

---

## Getting Help

### Cisco IOS Help
```cisco
?                               # Context-sensitive help
show ?                          # Available show commands
ip dhcp ?                       # DHCP configuration options
```

### Additional Resources
- Cisco Documentation: https://www.cisco.com/c/en/us/support/index.html
- CCNA Study Materials
- Network simulation tools (Packet Tracer, GNS3)
