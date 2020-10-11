# OSPF Deep Dive

This is the candidates guide. All of that good old OSPF, and then some.

## Topics & Overview

Some you've seen before, and others you haven't:

* Timers
* Link State Database (LSDB)
* Link State Advertisements (LSAs)
* Layer 3 traffic engineering
* Multi-area OSPF
* Autonomous System Boundary/Border Routers (ASBR)
* Area Border Rotuers (ABRs)
* Summarization at both ASBRs and ABRs
* Redistribution

## 

* Authentication
* Link Local Signaling (LLS)
* Stub router advertisement (max-metric router-lsa)
* Optimization of OSPF with:
    + Incremental Shortest Path First (ispf)
    + Bidirectional Forwarding Detenction
* OSPF and a Multiprotocol Label Switching (MPLS) BGP-free core
* OSPF and DMVPN
* RFC 2328
* RFC 5340
* RFC 5838

## 

* OSPF in the MPLS L3 VPN PE-CE use case
* Virtual Links
* Sham Links
* OSPF area types:
    + stub
    + totally stub
    + not-so-stubby (nssa)
    + totally not-so-stubby
* OSPFv3
* OSPFv3 w/ Address (AFs)

## 

## Additional Resources

* The following are some amazing resources on OSPF:
    + OSPF: Anatomy of an Internet Routing Protocol (Moy)
    + OSPF and IS-IS: Choosing an IGP for Large-Scal Networks (Doyle)
    + Routing TCP/IP Volume I (Doyle)
    + Cisco IP Routing (Zinin)
    + Cisco OSPF Command and Configuration Handbook (Parkhurst)
    + Implementing Cisco IP Routing 300-101 (Teare/Vachon/Graziani)
    + CCNP Routing and Switching TSHOOT 300-135 (Lacoste/Wallace)
    + IP Routing on Cisco IOS, IOS XE, and IOS XR (Edgeworth/Foos/Garcia Rios)
    + Troubleshooting IP Routing Protocol (Shamim/Aziz/Liu/Martey)
    + Cisco Configuration Guide
    + INE

## OSPF Router ID

* 32-bit dotted-decimal
    + Looks just like an IPv4 address even for OSPFv3
* The value '0.0.0.0' is reserved and cannot be used
* Router ID (RID) selection order of precendence:
    + Manually configured under the global OSPF configuration section
    + Highest numeric IPv4 address on a loopback interface
        + Cannot be shut down
    + Highest numeric IPv4 address on any non-loopback interface
        + Can even be in the DOWN/DOWN state, but cannot be shut down!
    + Multiple OSPF process on same node need unique RID values
        + This includes VRFs!

## Configuration & Verification Commands

* Configuration
    + router-id x.x.x.x
    + clear ip ospf process
* Verification
    + show ip ospf
    + show ip protocols
    + show ips ospf database
    + show ip ospf neighbor
    + debug ip ospf hello

## OSPF Hello Packets

* OSPF Packet Types:
    + Type 1: Hello
    + Type 2: Database Description (DBD)
    + Type 3: Link-State Request (LSR)
    + Type 4: Link-State Update (LSU)
    + Type 5: Link-State Acknowledgement (LSAck)
* Communicates
    + Network/Subnet Mask
    + Hello Interval
    + Options
    + Router Priority
    + Router dead interval
    + Designated Router (DR) / Backup Designated Router (BDR)
    + Neighbor
* Information exchanged via Hello Protocol

## 

* Hello packet is the first packet to be sent
* Ensures two-way communication exists
* Servers as a failure detection mechanism
* TTL = 1
* Adjustable
* Destination of 224.0.0.5 on IPv4 broadcast networks
    + FF02::5 on IPv6 networks
    + The 'All OSPF Router's multicast address
    + Layer 2 destination MAC address: 01:00:5E:00:00:05
    + Destination is unicast on non-broadcast networks

## 

| Network Type | Address | Hello | Dead |
|:-:|:-:|:-:|:-:|
|Broadcast (BR/BDR)| 224.0.0.5| 10 seconds| 40 seconds|
|Non-Broadcast (DR-BDR)| Unicast| 30 seconds| 120 seconds|
|Point-to-Point| 224.0.0.5| 10 seconds| 40 seconds|
|Point-to-Multipoint|224.0.0.5| 30 seconds|120 seconds|
|Point-to-Multipoint Non-Broadcast| Unicast| 30 seconds| 120 seconds|

* Easy way to remember the DR/BDR election:
    + If the word 'point' is in the name of the OSPF network type, there is no DR/BDR election.

### Configuration & Verification Commands

* Configuration
    + ip ospf hello-interval [# seconds]
    + ip ospf dead-interval [# seconds]
* Verification
    + show ip ospf interface [interface]
    + show ip ospf neighbor
    + debug ip ospf hello
