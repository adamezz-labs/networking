# Verification & Troubleshooting (TP1)

1. From BR1, ping BR2 G0/0/0 (192.168.33.250).
2. From BR2, ping BR1 G0/0/0 (192.168.33.249).
3. If ping fails:
   - Check IP address and mask on both interfaces.
   - Check `no shutdown` on interfaces.
   - Ensure no overlapping subnets were created.
   - Verify routing: if multiple routers exist, add static routes or enable a dynamic routing protocol.
