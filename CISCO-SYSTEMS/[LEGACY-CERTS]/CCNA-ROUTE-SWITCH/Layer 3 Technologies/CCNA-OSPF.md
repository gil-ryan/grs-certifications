# OSPF

## Introduction to OSPF

* OSPF is a link state routing protocol
* OSPF uses link state logic, which can be broken into three branches:
    + Neighbor discovery.
    + Topology database exchange.
    + Route computation.
* OSPF neighbors are discovered using Hello packets, just like EIGRP
* Hello packets are sent using multicast address 224.0.0.5
* OSPF does not use TCP or UDP for its transport; it uses IP 89
* OSPF uses concept of areas.

When using the __network__ command, a router must create a Type-1 Router LSA at the very least. A new prefix will be flooded to every link in its respective area. Rebuilding a SPF tree can be an intensive CPU process. If you receive a Type-1 LSA you need to tear it down and rebuild it. If I'm directly connected to a network, when I create a link state advertisement and flood it out. After a half hour, I'm going to flood it again. EIGRP does NOT do this. The second you create another area, the router becomes an __Area Border Router__. If you're not an ABR you cannot exchange routes between areas.

## 

* OSPF topology database, commonly known as _link state database_, is exchanged inside an area only.
* This detail topology information is not exchanged between areas
* OSPF has a hierarchical design.
* All areas must connect into the backbone (area 0)
* Prevent routing loops

## 


* ABRs connect non-backbone areas to the backbone area
* Instead of exchanging detailed topology information between areas, ABRs only advertise the subnets between areas.

An ABR will not share an entire network topology with the backbone, but if it learns about a new network prefix, it will advertise it. This is a Type-3 Summary LSA, and once its injected into Area 0, that's where it stays. The other ABRs will strip out that network information from the summary LSA and create a new one to give out.

## 

* Inside an area, all internal routers must have the same image of the network; that is, their link state databases must be the same.
* After the link state database is populated, shorest path first (SPF) is run on link state database (LSDB) to find the best, lowest cost, paths to all destinations.

* To configure OSPF on any router, we first enable OSPF process using the command __router ospf <process-id>
* Process ID is locally significant
* To advertise any particular subnet or to enable any interface for OSPF to discover neighbors on that interface, the __network__ command is used.
* __network__ [ip subnet] [wildcard-mask] __area__ <area-number>

* If multiple network commands match the same interface, the interface will be advertised in an area with a more specific wildcard mask (most 0s)
* The more specific command is always shown on the top in the running-configuration of the router.

## 

* OSPF uses the same concept as EIGRP to assign a router-ID for OSPF process:
    + manually configured router-ID is preferred over..
    + highest IP address of any up/up loopback interace, which is preferred over..
    + highest IP address of any up/up non-loopback interface
* OSPF also uses the concept of DR and BDR, which will be discussed.
