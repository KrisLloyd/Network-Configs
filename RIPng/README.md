# Example RIPng Network

A sample RIPng network.

## Network Topology

[![RIPng Topology](topology.png)]()


### Devices and Interfaces

| Device | Interface | IPv6 Address Unicast | IPv6 Address Link-Local |
| ------ | ------ | ------ | ------ |
| R1 | GigabitEthernet0/0 | 2001:db8:cafe:1::1/64 | fe80::1 |
|   | Serial0/0/0 | 2001:db8:cafe:2::1/64 | fe80::1 |
| R2 | GigabitEthernet0/0 | 2001:db8:cafe:3::1/64 | fe80::2 |
|   | Serial0/0/0 | 2001:db8:cafe:2::2/64 | fe80::2 |
|   | Serial0/0/1 | 2001:db8:feed:1::2/64 | fe80::2 |
|   | Loopback10 | 2001:db8:cafe:10::2/64 | fe80::2 |
|   | Loopback20 | 2001:db8:cafe:20::2/64 | fe80::2 |
|   | Loopback30 | 2001:db8:cafe:30::2/64 | fe80::2 |
| R3 | Serial0/0/1 | 2001:db8:feed:1::1/64 | fe80::3 |

## Technologies

* IPv6 addressing
* Type 9 Scrypt password encryption
* SSHv2 Access
* RIPng Routing


### RIPng

> This section is currently under construction. Sorry about that!

The routing table on R1 shows the learned routes from R2. The entries are shown as 'R' to indicate the source of the route is from the RIP protocol.
<pre>
R1#show ipv6 route
IPv6 Routing Table - 10 entries
Codes: C - Connected, L - Local, S - Static, R - RIP, B - BGP
       U - Per-user Static route, M - MIPv6
       I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea, IS - ISIS summary
       O - OSPF intra, OI - OSPF inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2
       D - EIGRP, EX - EIGRP external
R   ::/0 [120/2]
     via FE80::230:A3FF:FEC7:3699, Serial0/0/0
C   2001:DB8:CAFE:1::/64 [0/0]
     via GigabitEthernet0/0, directly connected
L   2001:DB8:CAFE:1::1/128 [0/0]
     via GigabitEthernet0/0, receive
C   2001:DB8:CAFE:2::/64 [0/0]
     via Serial0/0/0, directly connected
L   2001:DB8:CAFE:2::1/128 [0/0]
     via Serial0/0/0, receive
R   2001:DB8:CAFE:3::/64 [120/2]
     via FE80::230:A3FF:FEC7:3699, Serial0/0/0
R   2001:DB8:CAFE:10::/64 [120/2]
     via FE80::230:A3FF:FEC7:3699, Serial0/0/0
R   2001:DB8:CAFE:20::/64 [120/2]
     via FE80::230:A3FF:FEC7:3699, Serial0/0/0
R   2001:DB8:CAFE:30::/64 [120/2]
     via FE80::230:A3FF:FEC7:3699, Serial0/0/0
L   FF00::/8 [0/0]
     via Null0, receive
</pre>

The active routing protocols are shown below:

<pre>
R1#show ipv6 protocols
IPv6 Routing Protocol is "connected"
IPv6 Routing Protocol is "ND"
IPv6 Routing Protocol is "rip RIPNG"
  Interfaces:
    Serial0/0/0
    GigabitEthernet0/0
  Redistribution:
    None
</pre>

The RIP database displays the learned routes, their metrics, next-hop information, and timeouts.

<pre>
R1#show ipv6 rip database
RIP process "RIPNG" local RIB 
 ::/0, metric 2, installed
    Serial0/0/0/FE80::2, expires in 161 sec
 2001:DB8:CAFE:2::/64, metric 2
    Serial0/0/0/FE80::2, expires in 161 sec
 2001:DB8:CAFE:3::/64, metric 2, installed
    Serial0/0/0/FE80::2, expires in 161 sec
 2001:DB8:CAFE:10::/64, metric 2, installed
    Serial0/0/0/FE80::2, expires in 161 sec
 2001:DB8:CAFE:20::/64, metric 2, installed
    Serial0/0/0/FE80::2, expires in 161 sec
 2001:DB8:CAFE:30::/64, metric 2, installed
    Serial0/0/0/FE80::2, expires in 161 sec
</pre>

## Authors

* **Kristopher Lloyd** - [LinkedIn](https://www.linkedin.com/in/kris-lloyd)
