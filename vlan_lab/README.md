# VLAN & Trunking Lab

## Topology
Two Cisco switches (S1, S2) connected via trunk.
Three VLANs for users and two special VLANs.

## VLANs
- VLAN 10: gestion
- VLAN 20: Commercial
- VLAN 30: Operations
- VLAN 999: Parking Lot
- VLAN 1000: Native VLAN

## PCs
- PC-A: VLAN 20 → 192.168.20.12
- PC-B: VLAN 30 → 192.168.30.13
- PC-C: VLAN 10 → 192.168.10.14

## Notes
- Native VLAN is 1000
- No inter-VLAN routing (Layer 2 only)
