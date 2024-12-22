```
PC6> ping 150.99.0.1
*192.168.1.1 icmp_seq=1 ttl=254 time=50.749 ms (ICMP type:3, code:1, Destination host unreachable)
*192.168.1.1 icmp_seq=2 ttl=254 time=46.219 ms (ICMP type:3, code:1, Destination host unreachable)
*192.168.1.1 icmp_seq=3 ttl=254 time=47.266 ms (ICMP type:3, code:1, Destination host unreachable)
*192.168.1.1 icmp_seq=4 ttl=254 time=46.743 ms (ICMP type:3, code:1, Destination host unreachable)
*192.168.1.1 icmp_seq=5 ttl=254 time=55.349 ms (ICMP type:3, code:1, Destination host unreachable)
```
-**192.168.1.1** is the IP address of the device that generated the **ICMP Destination Unreachable** message.
-This device (likely your router or gateway) is reporting that it cannot forward packets to **150.99.0.1**.

Cause : NAT translation on that router is wrongly configured. More precisely, I configured 255.255.255.255 as the pool's netmask.
Solution : Minimum netmask when configuring NAT pools is 255.255.255.252
```
ip nat pool PAT-POOL1 142.99.5.1 142.99.5.1 netmask 255.255.255.252
```

--------------------------------------------------------------------------
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17348476650004okyrv.png)
Even after doing no shut, protocol is still labeled as down.

Cause : Gns3 retarded
Solution : Shut no shut