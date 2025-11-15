# IPv6 Configuration Steps (TP2)

## Addressing Table

-   **R1 G0/0** --- `2001:db8:acad:1::1/64`
-   **R1 G0/1** --- `2001:db8:acad:a::1/64`
-   **S1 VLAN1** --- `2001:db8:acad:1::b/64`
-   **PC-A** --- `2001:db8:acad:a::3/64` (static), gateway: R1 G0/1\
-   **PC-B** --- `2001:db8:acad:1::3/64` (static), gateway: R1 G0/0

------------------------------------------------------------------------

## 1. Switch SDM Model (Cisco 2960)

If IPv6 SVI is not supported, enable dual-stack SDM:

``` bash
S1(config)# sdm prefer dual-ipv4-and-ipv6 default
S1# reload
```

------------------------------------------------------------------------

## 2. Basic Device Configuration

-   Set hostname\
-   Disable DNS lookup\
-   Set passwords\
-   Enable `service password-encryption`\
-   Configure login banners

------------------------------------------------------------------------

## 3. Configure IPv6 on Router R1

### Interface G0/0

``` bash
interface GigabitEthernet0/0
 description LAN A / link to PC-B
 ipv6 address 2001:db8:acad:1::1/64
 ipv6 address fe80::1 link-local
 no shutdown
```

### Interface G0/1

``` bash
interface GigabitEthernet0/1
 description LAN 1 / link to switch
 ipv6 address 2001:db8:acad:a::1/64
 ipv6 address fe80::1 link-local
 no shutdown
```

> You can reuse `fe80::1` because link‑local addresses only apply to the
> local link.

------------------------------------------------------------------------

## 4. Enable IPv6 Routing

``` bash
R1(config)# ipv6 unicast-routing
```

------------------------------------------------------------------------

## 5. Configure IPv6 SVI on Switch S1

``` bash
interface vlan 1
 ipv6 address 2001:db8:acad:1::b/64
 ipv6 address fe80::b link-local
 no shutdown
```

------------------------------------------------------------------------

## 6. PC IPv6 Configuration

### PC-A

-   IP: `2001:db8:acad:a::3/64`\
-   Gateway: `fe80::1` or `2001:db8:acad:a::1`

### PC-B

-   IP: `2001:db8:acad:1::3/64`\
-   Gateway: `2001:db8:acad:1::1`

------------------------------------------------------------------------

## 7. Verification

``` bash
show ipv6 interface brief
```

Ping using link-local (router requires outgoing interface):

``` bash
ping ipv6 fe80::1
```

Traceroute:

``` bash
traceroute ipv6 <address>
```

------------------------------------------------------------------------

## Reflection Questions

### Why can the same link-local address be used on multiple interfaces?

Because link-local IPv6 addresses only have meaning on their local link,
so uniqueness across interfaces is not required.

### Subnet ID of `2001:db8:acad::aaaa:1234/64`

The /64 network portion is:

**`2001:db8:acad::/64`**
