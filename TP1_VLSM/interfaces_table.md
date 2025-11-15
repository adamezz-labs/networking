# Interfaces and Assigned Addresses (TP1)

| Device |  Description | Address | Mask | CIDR | Notes |
|--------|-----------|-------------|---------|------|------|
| BR1    |  BR1 — BR2 link | 192.168.33.249 | 255.255.255.252 | /30 | first usable in 192.168.33.248/30 |
| BR1    |  BR1 LAN (40 hosts) | 192.168.33.129 | 255.255.255.192 | /26 | first usable in 192.168.33.128/26 |
| BR2    | BR1 — BR2 link | 192.168.33.250 | 255.255.255.252 | /30 | second usable in link |
| BR2    | BR2 LAN (25 hosts) | 192.168.33.193 | 255.255.255.224 | /27 | first usable in 192.168.33.192/27 |

Other subnets available for BR2:
- BR2 small LAN (5 hosts): `192.168.33.224/29` (usable `.225`–`.230`)
- BR2 CCTV LAN (4 hosts): `192.168.33.232/29` (usable `.233`–`.238`)
- BR2 C2LAN (4 hosts): `192.168.33.240/29` (usable `.241`–`.246`)

**Broadcast addresses:**
- BR1 LAN: `192.168.33.191`
- BR2 LAN: `192.168.33.223`
- Link BR1-BR2: `192.168.33.251`

