# PC IPv6 static examples (TP2)

## PC-A
- IPv6: 2001:db8:acad:1::3/64
- Gateway: 2001:db8:acad:1::1  (or use link-local fe80::1 with proper scope)

## PC-B
- IPv6: 2001:db8:acad:a::3/64
- Gateway: 2001:db8:acad:a::1  (or use link-local fe80::1 with proper scope)

On Windows use:
- IPv6 static: Network Adapter -> Properties -> Internet Protocol Version 6 (TCP/IPv6) -> Use the following IPv6 address.
- Use `ipconfig` and `ping -6` / `tracert -6` for testing.
