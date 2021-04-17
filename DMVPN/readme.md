[< Back to Network-Configs](https://github.com/KrisLloyd/Network-Configs/)
***


# DMVPN, IPSec, OSPF

A simple DMVPN topology with IPSec.

## Network Topology

[![RIPng Topology](topology.png)]()


### Devices and Interfaces

| Device | Interface | IPv4 Address |
| ------ | ------ | ------ |
| R1 | GigabitEthernet0/0/0 | 10.1.2.1/24 | 
|   | GigabitEthernet0/0/1 | 10.1.3.1/24 |
|   | Loopback11 | 10.10.1.1/32 |
|   | Loopback12 | 10.10.1.2/32 |
|   | Loopback13 | 10.10.1.3/32 |
| R2 | GigabitEthernet0/0/0 | 10.1.2.2/24 | 
|   | GigabitEthernet0/0/1 | 10.2.3.2/24 |
|   | Loopback21 | 10.10.2.1/32 |
|   | Loopback22 | 10.10.2.2/32 |
|   | Loopback23 | 10.10.2.3/32 |
| R3 | GigabitEthernet0/0/0 | 10.1.3.3/24 | 
|   | GigabitEthernet0/0/1 | 10.2.3.3/24 |
|   | Loopback31 | 10.10.3.1/32 |
|   | Loopback32 | 10.10.3.2/32 |
|   | Loopback33 | 10.10.3.3/32 |

## Technologies

* EIGRP routing
* Type 9 Scrypt password encryption
* SSHv2 Access
* Load Balancing


### EIGRP


The **show ip eigrp neighbors** command shows that the network has reached convergence and all routers have formed neighbour adjacencies with their neighbours.

<pre>
R1#show ip eigrp neighbors
IP-EIGRP neighbors for process 100
H   Address         Interface      Hold Uptime    SRTT   RTO   Q   Seq
                                   (sec)          (ms)        Cnt  Num
0   10.1.2.2        Gig0/0/0     13   00:03:11  40     1000  0   15
1   10.1.3.3        Gig0/0/1     10   00:02:36  40     1000  0   22
</pre>

<pre>
R2#sh ip eigrp neighbors
IP-EIGRP neighbors for process 100
H   Address         Interface      Hold Uptime    SRTT   RTO   Q   Seq
                                   (sec)          (ms)        Cnt  Num
0   10.1.2.1        Gig0/0/1     12   00:03:44  40     1000  0   21
1   10.2.3.3        Gig0/0/0     13   00:03:08  40     1000  0   21
</pre>

<pre>
R3#sh ip eigrp neighbors
IP-EIGRP neighbors for process 100
H   Address         Interface      Hold Uptime    SRTT   RTO   Q   Seq
                                   (sec)          (ms)        Cnt  Num
0   10.1.3.1        Gig0/0/0     12   00:03:39  40     1000  0   22
1   10.2.3.2        Gig0/0/1     14   00:03:39  40     1000  0   16
</pre>

When the network has converged, the router builds the EIGRP topology table. The topology table contains a complete listing of all received routes from participating routers, however only the best routes are chosen to be used in the routing table. The topology table from R1 is shown below.

<pre>
R1#show ip eigrp topology
IP-EIGRP Topology Table for AS 100/ID(1.1.1.1)

Codes: P - Passive, A - Active, U - Update, Q - Query, R - Reply,
       r - Reply status

P 10.1.2.0/24, 1 successors, FD is 2816
         via Connected, GigabitEthernet0/0/0
P 10.1.3.0/24, 1 successors, FD is 2816
         via Connected, GigabitEthernet0/0/1
P 10.2.3.0/24, 2 successors, FD is 3072
         via 10.1.2.2 (3072/2816), GigabitEthernet0/0/0
         via 10.1.3.3 (3072/2816), GigabitEthernet0/0/1
P 10.10.1.1/32, 1 successors, FD is 128256
         via Connected, Loopback11
P 10.10.1.2/32, 1 successors, FD is 128256
         via Connected, Loopback12
P 10.10.1.3/32, 1 successors, FD is 128256
         via Connected, Loopback13
P 10.10.2.1/32, 1 successors, FD is 130816
         via 10.1.2.2 (130816/128256), GigabitEthernet0/0/0
P 10.10.2.2/32, 1 successors, FD is 130816
         via 10.1.2.2 (130816/128256), GigabitEthernet0/0/0
P 10.10.2.3/32, 1 successors, FD is 130816
         via 10.1.2.2 (130816/128256), GigabitEthernet0/0/0
P 10.10.3.1/32, 1 successors, FD is 130816
         via 10.1.3.3 (130816/128256), GigabitEthernet0/0/1
P 10.10.3.2/32, 1 successors, FD is 130816
         via 10.1.3.3 (130816/128256), GigabitEthernet0/0/1
P 10.10.3.3/32, 1 successors, FD is 130816
         via 10.1.3.3 (130816/128256), GigabitEthernet0/0/1
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
D       10.2.3.0/24 [90/3072] via 10.1.2.2, 00:02:58, GigabitEthernet0/0/0
                    [90/3072] via 10.1.3.3, 00:02:57, GigabitEthernet0/0/1
C       10.10.1.1/32 is directly connected, Loopback11
C       10.10.1.2/32 is directly connected, Loopback12
C       10.10.1.3/32 is directly connected, Loopback13
D       10.10.2.1/32 [90/130816] via 10.1.2.2, 00:03:01, GigabitEthernet0/0/0
D       10.10.2.2/32 [90/130816] via 10.1.2.2, 00:03:01, GigabitEthernet0/0/0
D       10.10.2.3/32 [90/130816] via 10.1.2.2, 00:03:01, GigabitEthernet0/0/0
D       10.10.3.1/32 [90/130816] via 10.1.3.3, 00:02:57, GigabitEthernet0/0/1
D       10.10.3.2/32 [90/130816] via 10.1.3.3, 00:02:57, GigabitEthernet0/0/1
D       10.10.3.3/32 [90/130816] via 10.1.3.3, 00:02:57, GigabitEthernet0/0/1
</pre>

Performing the traceroute command shows that both paths are viable options.

<pre>
R1#traceroute 10.2.3.3
Type escape sequence to abort.
Tracing the route to 10.2.3.3

  1   10.1.2.2        0 msec    0 msec    0 msec    
R1#traceroute 10.2.3.3
Type escape sequence to abort.
Tracing the route to 10.2.3.3

  1   10.1.3.3        1 msec    0 msec    0 msec
</pre>

To demonstrate unequal-cost EIGRP load balancing, I'll reuce the bandwitdth on the R1-R2 link.

<pre>
R1(config)#int g0/0/0
R1(config-if)#bandwidth 500000
</pre>
<pre>
R2(config)#int g0/0/0
R2(config-if)#bandwidth 500000
</pre>

We can now see that the entry in the route table for destination 10.2.3.0/24 only has a single entry. This is because the route to that destination via 10.1.3.3 has the nowest cost and was selected by the DUAL algorith to be placed into the route table over the other route via 10.1.2.2.

<pre>
R1#sh ip ro
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
D       10.2.3.0/24 [90/3072] via 10.1.3.3, 00:24:24, GigabitEthernet0/0/1
C       10.10.1.1/32 is directly connected, Loopback11
C       10.10.1.2/32 is directly connected, Loopback12
C       10.10.1.3/32 is directly connected, Loopback13
D       10.10.2.1/32 [90/131072] via 10.1.3.3, 00:10:44, GigabitEthernet0/0/1
D       10.10.2.2/32 [90/131072] via 10.1.3.3, 00:10:44, GigabitEthernet0/0/1
D       10.10.2.3/32 [90/131072] via 10.1.3.3, 00:10:44, GigabitEthernet0/0/1
D       10.10.3.1/32 [90/130816] via 10.1.3.3, 00:24:24, GigabitEthernet0/0/1
D       10.10.3.2/32 [90/130816] via 10.1.3.3, 00:24:24, GigabitEthernet0/0/1
D       10.10.3.3/32 [90/130816] via 10.1.3.3, 00:24:24, GigabitEthernet0/0/1
</pre>

To regain load balancing, we have to apply an EIGRP feature called **variance** which allows for the DUAL algorithm to consider routes that have a cost with a degree of variance. The value in the cariance command is a multiplier for the base metric of a learned route, and the default value is 1. By setting the variance value higher, we can manipulate the metrics such that the lower bandwidth link is considered equal to the faster link. 

<pre>
R1(config)#router eigrp 100
R1(config)#variance 2
</pre>

We can now see that the route to 10.2.3.0/24 now has its load balancing reenabled, however changing the variance has also included the multiple paths to other knows routes, which may be sub-optimal.

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
D       10.2.3.0/24 [90/3072] via 10.1.3.3, 00:04:50, GigabitEthernet0/0/1
C       10.10.1.1/32 is directly connected, Loopback11
C       10.10.1.2/32 is directly connected, Loopback12
C       10.10.1.3/32 is directly connected, Loopback13
D       10.10.2.1/32 [90/131072] via 10.1.3.3, 00:04:35, GigabitEthernet0/0/1
                     [90/133376] via 10.1.2.2, 00:04:33, GigabitEthernet0/0/0
D       10.10.2.2/32 [90/131072] via 10.1.3.3, 00:04:35, GigabitEthernet0/0/1
                     [90/133376] via 10.1.2.2, 00:04:33, GigabitEthernet0/0/0
D       10.10.2.3/32 [90/131072] via 10.1.3.3, 00:04:35, GigabitEthernet0/0/1
                     [90/133376] via 10.1.2.2, 00:04:33, GigabitEthernet0/0/0
D       10.10.3.1/32 [90/130816] via 10.1.3.3, 00:04:50, GigabitEthernet0/0/1
D       10.10.3.2/32 [90/130816] via 10.1.3.3, 00:04:50, GigabitEthernet0/0/1
D       10.10.3.3/32 [90/130816] via 10.1.3.3, 00:04:50, GigabitEthernet0/0/1
</pre>

To check what the current variance level is, use the command **show ip protocols**

<pre>
R1#show ip protocols

Routing Protocol is "eigrp  100 " 
  Outgoing update filter list for all interfaces is not set 
  Incoming update filter list for all interfaces is not set 
  Default networks flagged in outgoing updates  
  Default networks accepted from incoming updates 
  EIGRP metric weight K1=1, K2=0, K3=1, K4=0, K5=0
  EIGRP maximum hopcount 100
  EIGRP maximum metric variance 2
Redistributing: eigrp 100
  Automatic network summarization is in effect  
  Automatic address summarization: 
  Maximum path: 4
  Routing for Networks:  
     10.0.0.0
  Routing Information Sources:  
    Gateway         Distance      Last Update 
    10.1.3.3        90            2514310    
    10.1.2.2        90            2531189    
  Distance: internal 90 external 170
</pre>





## Author

* **Kristopher Lloyd** - [LinkedIn](https://www.linkedin.com/in/kris-lloyd)
