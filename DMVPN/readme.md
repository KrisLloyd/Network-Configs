[< Back to Network-Configs](https://github.com/KrisLloyd/Network-Configs/)
***


# DMVPN, IPSec, OSPF

A simple DMVPN topology with IPSec.

## Network Topology

[![DMVPN Topology](topology.png)]()


### Devices and Interfaces

| Device | Interface | IPv4 Address |
| ------ | ------ | ------ |
| Hub | GigabitEthernet0/0 | 172.16.0.1/24 | 
|   | Tunnel 100 | 10.0.0.100/24 |
|   | Loopback10 | 10.255.255.100/32 |
| R2 | GigabitEthernet0/0/0 | 172.16.0.2/24 | 
|   | Tunnel 100 | 10.0.0.1/24 |
|   | Loopback10 | 10.255.255.1/32 |
| R3 | GigabitEthernet0/0/0 | 172.16.0.3/24 | 
|   | Tunnel 100 | 10.0.0.2/24 |
|   | Loopback10 | 10.255.255.2/32 |

## Technologies

* OSPF routing
* Type 9 Scrypt password encryption
* SSHv2 Access
* DMVPN
* VRF
* IPSec


### DMVPN

#### DMVPN Phase 1

Configure the interfaces as defined in the diagram. The interfaces connected to the switch (representing the internet) will be placed into a VRF called 'underlay' for better organization. The oputput below shows the VRF routing table of the **Hub** router, as well as pings to confirm connectivity.

<pre>
Hub#show ip route vrf underlay

Routing Table: underlay
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is not set

      172.16.0.0/16 is variably subnetted, 2 subnets, 2 masks
C        172.16.0.0/24 is directly connected, GigabitEthernet0/0
L        172.16.0.100/32 is directly connected, GigabitEthernet0/0
Hub#ping vrf underlay 172.16.0.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.16.0.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/4 ms
Hub#ping vrf underlay 172.16.0.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.16.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/4 ms
</pre>

With the basics configured, the default VRF which will be used by the private network won't use the route table of the public underlay VRF. The next configuration step is to build the GRE tunnels that will be used by NHRP for DMVPN.

<pre>
Hub(config) #interface Tunnel100
Hub(config-if) #ip address 10.0.0.100 255.255.255.0
Hub(config-if) #ip nhrp authentication cisco
 ip nhrp map multicast dynamic
 ip nhrp network-id 10
 ip nhrp holdtime 60
 ip nhrp registration timeout 10
 tunnel source GigabitEthernet0/0
 tunnel mode gre multipoint
 tunnel key 1
 tunnel vrf underlay
</pre>





#### DMVPN Phase 2

<pre>
Code output segments here
</pre>

#### IPSec

<pre>
Code output segments here
</pre>




## Author

* **Kristopher Lloyd** - [LinkedIn](https://www.linkedin.com/in/kris-lloyd)
