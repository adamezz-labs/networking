# IPv6 Configuration Steps (TP2)

**Addressing table (from the TP):**
- R1 G0/0 — `2001:db8:acad:1::1/64`
- R1 G0/1 — `2001:db8:acad:a::1/64`
- S1 VLAN1 — `2001:db8:acad:1::b/64` (SVI)
- PC-A — `2001:db8:acad:a::3/64` (static) — gateway: R1 G0/1
- PC-B — `2001:db8:acad:1::3/64` (static) — gateway: R1 G0/0

**Notes & steps:**

1. **Switch SDM model (if using Cisco 2960)**
   - The default SDM template may not support IPv6 SVI. Run:
     ```
     S1(config)# sdm prefer dual-ipv4-and-ipv6 default
     S1# reload
     ```
   - After reload, SVI IPv6 will be available.

2. **Configure base settings on R1 and S1**
   - Set hostname, disable domain lookup, set passwords, service password-encryption, banners ).

3. **Assign IPv6 addresses on R1:**
   ```
 interface GigabitEthernet0/0
 description LAN A / link to PC-B
 ipv6 address 2001:db8:acad:1::3/64
 ipv6 address fe80::1 link-local
 no shutdown
```

interface GigabitEthernet0/1
 description LAN 1 / link to switch
 ipv6 address 2001:db8:acad:a::1/64
 ipv6 address fe80::1 link-local
 no shutdown

   *You may reuse the same link-local `fe80::1` on multiple router interfaces — link-local scope is per-link.*

4. **Enable IPv6 routing on the router**
   ```
   R1(config)# ipv6 unicast-routing
   ```

5. **Configure SVI on the switch (S1)**
   ```
 interface vlan 1
 ipv6 address 2001:db8:acad:1::b/64
 ipv6 address fe80::b link-local
    no shutdown
   ```

6. **PC IPv6 addresses (static examples)**
   - PC-A:
     - IP: `2001:db8:acad:a::3/64`
     - Gateway: (link-local or global) - often `fe80::1` if using link-local of R1 or `2001:db8:acad:1::1`
   - PC-B:
     - IP: `2001:db8:acad:1::3/64`
     - Gateway: `2001:db8:acad:a::1`

   On Windows, `ipconfig /all` will show both SLAAC-generated addresses and static ones.

7. **Verify**
   - `show ipv6 interface brief` on R1 and S1
   - `ping` using link-local addresses (must specify exit interface on routers): `ping ipv6 fe80::1` from host with proper scope
   - `traceroute` to verify end-to-end

**Reflection questions (answered):**
- *Why can the same link-local address `fe80::1` be assigned to two router interfaces?*  
  Because link-local addresses are only valid on the local link; they do not need to be globally unique across different links.
- *What is the subnet ID of `2001:db8:acad::aaaa:1234/64`?*  
  With a /64 prefix, the subnet ID is the 64-bit network part: `2001:db8:acad::/64` — the host portion is the tail `aaaa:1234`.
