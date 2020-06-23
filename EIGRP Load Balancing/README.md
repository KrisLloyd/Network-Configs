[< Back to Network-Configs](https://github.com/KrisLloyd/Network-Configs/)
***


# EIGRP Network with Load Balancing

A sample EIGRP network with redundant links to leverage load balancing.

## Network Topology

[![RIPng Topology](topology.png)]()


### Devices and Interfaces

| Device | Interface | IPv4 Address |
| ------ | ------ | ------ |
| R1 | Serial0/0/0 | 10.1.2.1/24 | 
|   | Serial0/0/1 | 10.1.3.1/24 |
|   | Loopback11 | 10.10.1.1/32 |
|   | Loopback12 | 10.10.1.2/32 |
|   | Loopback13 | 10.10.1.3/32 |
| R2 | Serial0/0/0 | 10.1.2.2/24 | 
|   | Serial0/0/1 | 10.2.3.2/24 |
|   | Loopback21 | 10.10.2.1/32 |
|   | Loopback22 | 10.10.2.2/32 |
|   | Loopback23 | 10.10.2.3/32 |
| R3 | Serial0/0/0 | 10.1.3.3/24 | 
|   | Serial0/0/1 | 10.2.3.3/24 |
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

## Author

* **Kristopher Lloyd** - [LinkedIn](https://www.linkedin.com/in/kris-lloyd)
