[< Back to Network-Configs](https://github.com/KrisLloyd/Network-Configs/)
***


# EIGRP Network with Load Balancing

A sample EIGRP network with redundant links to leverage load balancing.

## Network Topology

[![RIPng Topology](topology.png)]()


### Devices and Interfaces

| Device | Interface | IPv4 Address |
| ------ | ------ | ------ |
| R1 | GigabitEthernet0/0 | 10.1.2.1/24 | 
|   | GigabitEthernet0/1 | 10.1.3.1/24 |
|   | Loopback11 | 10.10.1.1/32 |
|   | Loopback12 | 10.10.1.2/32 |
|   | Loopback13 | 10.10.1.3/32 |
| R2 | GigabitEthernet0/0 | 10.1.2.2/24 | 
|   | GigabitEthernet0/1 | 10.2.3.2/24 |
|   | Loopback21 | 10.10.2.1/32 |
|   | Loopback22 | 10.10.2.2/32 |
|   | Loopback23 | 10.10.2.3/32 |
| R3 | GigabitEthernet0/0 | 10.1.3.3/24 | 
|   | GigabitEthernet0/1 | 10.2.3.3/24 |
|   | Loopback31 | 10.10.3.1/32 |
|   | Loopback32 | 10.10.3.2/32 |
|   | Loopback33 | 10.10.3.3/32 |

## Technologies

* EIGRP routing
* Type 9 Scrypt password encryption
* SSHv2 Access
* Load Balancing


### EIGRP

> This section is under construction. Sorry about that!

The **show ip eigrp neighbors** command shows that the network has reached convergence and all routers have formed neighbour adjacencies with their neighbours.

<pre>
R1#show ip eigrp neighbors
IP-EIGRP neighbors for process 100
H   Address         Interface      Hold Uptime    SRTT   RTO   Q   Seq
                                   (sec)          (ms)        Cnt  Num
0   10.1.2.2        Gig0/0       13   00:03:11  40     1000  0   15
1   10.1.3.3        Gig0/1       10   00:02:36  40     1000  0   22
</pre>

<pre>
R2#sh ip eigrp neighbors
IP-EIGRP neighbors for process 100
H   Address         Interface      Hold Uptime    SRTT   RTO   Q   Seq
                                   (sec)          (ms)        Cnt  Num
0   10.1.2.1        Gig0/1       12   00:03:44  40     1000  0   21
1   10.2.3.3        Gig0/0       13   00:03:08  40     1000  0   21
</pre>

<pre>
R3#sh ip eigrp neighbors
IP-EIGRP neighbors for process 100
H   Address         Interface      Hold Uptime    SRTT   RTO   Q   Seq
                                   (sec)          (ms)        Cnt  Num
0   10.1.3.1        Gig0/0       12   00:03:39  40     1000  0   22
1   10.2.3.2        Gig0/1       14   00:03:39  40     1000  0   16
</pre>

When the network has converged, the router builds the EIGRP topology table. The topology table contains a complete listing of all received routes from participating routers, however only the best routes are chosen to be used in the routing table. The topology table from R1 is shown below.

<pre>
R1#show ip eigrp topology
IP-EIGRP Topology Table for AS 100/ID(1.1.1.1)

Codes: P - Passive, A - Active, U - Update, Q - Query, R - Reply,
       r - Reply status

P 10.1.2.0/24, 1 successors, FD is 2816
         via Connected, GigabitEthernet0/0
P 10.1.3.0/24, 1 successors, FD is 2816
         via Connected, GigabitEthernet0/1
P 10.2.3.0/24, 2 successors, FD is 3072
         via 10.1.2.2 (3072/2816), GigabitEthernet0/0
         via 10.1.3.3 (3072/2816), GigabitEthernet0/1
P 10.10.1.1/32, 1 successors, FD is 128256
         via Connected, Loopback11
P 10.10.1.2/32, 1 successors, FD is 128256
         via Connected, Loopback12
P 10.10.1.3/32, 1 successors, FD is 128256
         via Connected, Loopback13
P 10.10.2.1/32, 1 successors, FD is 130816
         via 10.1.2.2 (130816/128256), GigabitEthernet0/0
P 10.10.2.2/32, 1 successors, FD is 130816
         via 10.1.2.2 (130816/128256), GigabitEthernet0/0
P 10.10.2.3/32, 1 successors, FD is 130816
         via 10.1.2.2 (130816/128256), GigabitEthernet0/0
P 10.10.3.1/32, 1 successors, FD is 130816
         via 10.1.3.3 (130816/128256), GigabitEthernet0/1
P 10.10.3.2/32, 1 successors, FD is 130816
         via 10.1.3.3 (130816/128256), GigabitEthernet0/1
P 10.10.3.3/32, 1 successors, FD is 130816
         via 10.1.3.3 (130816/128256), GigabitEthernet0/1
</pre>

The route table contains the best possible route for a given network. From the topology table above, the router learned 2 different methods to reach the 10.2.3.0/24 network. Since there is no difference in the cost (as seen by EIGRP), both are viable options and are inserted into the route table.

<pre>
R1#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 14 subnets, 2 masks
C       10.1.2.0/24 is directly connected, GigabitEthernet0/0/0
L       10.1.2.1/32 is directly connected, GigabitEthernet0/0/0
C       10.1.3.0/24 is directly connected, GigabitEthernet0/0/1
L       10.1.3.1/32 is directly connected, GigabitEthernet0/0/1
D       10.2.3.0/24 [90/3072] via 10.1.2.2, 00:16:24, GigabitEthernet0/0/0
                    [90/3072] via 10.1.3.3, 00:15:48, GigabitEthernet0/0/1
C       10.10.1.1/32 is directly connected, Loopback11
C       10.10.1.2/32 is directly connected, Loopback12
C       10.10.1.3/32 is directly connected, Loopback13
D       10.10.2.1/32 [90/130816] via 10.1.2.2, 00:16:24, GigabitEthernet0/0/0
D       10.10.2.2/32 [90/130816] via 10.1.2.2, 00:16:24, GigabitEthernet0/0/0
D       10.10.2.3/32 [90/130816] via 10.1.2.2, 00:16:24, GigabitEthernet0/0/0
D       10.10.3.1/32 [90/130816] via 10.1.3.3, 00:15:48, GigabitEthernet0/0/1
D       10.10.3.2/32 [90/130816] via 10.1.3.3, 00:15:48, GigabitEthernet0/0/1
D       10.10.3.3/32 [90/130816] via 10.1.3.3, 00:15:48, GigabitEthernet0/0/1
</pre>


## Author

* **Kristopher Lloyd** - [LinkedIn](https://www.linkedin.com/in/kris-lloyd)
