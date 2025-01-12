1. Configure Topology as above.
2. Ensure loopback end to end connectivity.
3. Enable SSH on all routers with cisco:cisco
4. Add private network at R3 and use NAT to resolve private to public ip addresses.
5. Create a GRE tunnel interconnecting both R3.
6. Create a VPN tunnel at the default gateway(R2). Make sure static routes exist between R2 and R1.
7. Configure NTP for all devices. Make R1 as NTP master.
8. Configure PRTG on lab PC with SNMPv3 to monitor traffic.

## Enable SSH on all routers
