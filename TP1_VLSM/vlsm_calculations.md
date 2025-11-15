# VLSM Calculations (TP1)

**Given:**
- Network: `192.168.33.128/25` (usable addresses `.129` to `.254`, broadcast `.255`)
- Required subnets (from worksheet):
  - BR1 LAN: 40 hosts
  - BR2 LAN: 25 hosts
  - BR2 LAN (small): 5 hosts
  - BR2 CCTV LAN: 4 hosts
  - BR2 C2LAN: 4 hosts
  - BR1-BR2 link: 2 hosts

Start at the first available address: `192.168.33.128`.

1. **BR1 LAN — /26**
   - Network: `192.168.33.128/26`
   - Range: `192.168.33.128` — `192.168.33.191`
   - Usable hosts: `192.168.33.129` — `192.168.33.190`
   - Broadcast: `192.168.33.191`
   - Hosts available: 62 (enough for 40 hosts)

2. **BR2 LAN (25 hosts) — /27** (next free block starts at .192)
   - Network: `192.168.33.192/27`
   - Range: `192.168.33.192` — `192.168.33.223`
   - Usable: `192.168.33.193` — `192.168.33.222`
   - Broadcast: `192.168.33.223`
   - Hosts: 30

3. **BR2 LAN (5 hosts) — /29** (next free block starts at .224)
   - Network: `192.168.33.224/29`
   - Range: `192.168.33.224` — `192.168.33.231`
   - Usable: `192.168.33.225` — `192.168.33.230`
   - Broadcast: `192.168.33.231`
   - Hosts: 6

4. **BR2 CCTV LAN (4 hosts) — /29** (next at .232)
   - Network: `192.168.33.232/29`
   - Usable: `192.168.33.233` — `192.168.33.238`
   - Broadcast: `192.168.33.239`

5. **BR2 C2LAN (4 hosts) — /29** (next at .240)
   - Network: `192.168.33.240/29`
   - Usable: `192.168.33.241` — `192.168.33.246`
   - Broadcast: `192.168.33.247`

6. **BR1-BR2 link — /30** (next at .248)
   - Network: `192.168.33.248/30`
   - Usable: `192.168.33.249` — `192.168.33.250`
   - Broadcast: `192.168.33.251`

**Remaining addresses:** `192.168.33.252` — `192.168.33.255` (small remainder /30 or /30 + /32 depending on use)

**Notes:**
- I allocated largest subnets first to reduce fragmentation.
