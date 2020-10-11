# EIGRP 

## Overview 

### EIGRP Multicast Address: 224.0.0.10

* advanced distance vector protocol
	+ sometimes called a hybrid routing protocol
* a classless routing protocol
* cisco proprietary

### Types of EIGRP Packets

* Hello
* Update
* Acknowledge

## Adminstrative Distance

The AD for an External EIGRP route (EX) is 170. An internal EIGRP route is 90. 

For your convenience, here are all of them:

| Type| AD| 
|:-:|:-:|
|Connected interface| 0|
|Static route |1|
|EIGRP | 90|
|OSPF |110|
|RIP |120|
|External EIGRP|170|
|Unknown|255|

## Diffusing Update Algorithm

EIGRP uses DUAL to:

* determine best loop-free path
* determine backup loop-free path
* provide fast convergence

By default, EIGRP uses bandwidth and delay for metric calculation. EIGRP has 3 steps to provide network convergence:

 1. Neighbor relationship
 2. Topology exchange information
 3. DUAL to populate the routing table
 
* EIGRP uses hello packets to determine neighbors.
* To enable EIGRP on any interface of a router/L3 switch, use the __network__ command under EIGRP configuration mode.
* The EIGRP AS number must be the same between routers trying to become neighbors with each other.

## Split Horizon

Prevents routing loops by telling router never to receive a packet on the interface it is sent out of.

However there is an exception in _update_ packets acknowledging an initial _hello_. This is called a __poison reverse__, where the router will indicate the _delay_ as Infinity. Basically saying, "That route you sent me, you cannot get to it from me, I know __you__ have the route.".

## K-Values

EIGRP uses a rather complex formula, __bandwidth__, __delay__, __reliability__, __load__, __mtu__. How do you display the _k-values_?

```
R4#show ip protocols
<...>
  EIGRP-IPv4 Protocol for AS(10)
    Metric weight K1=1, K2=0, K3=1, K4=0, K5=0
<...>
R4(config-router)#metric weights 1 0 1 1 1 1
R4(config-router)#
*Feb  4 15:35:14.463: %DUAL-5-NBRCHANGE: EIGRP-IPv4 10: Neighbor 159.0.0.1 (GigabitEthernet2/0) is down: metric changed
*Feb  4 15:35:15.143: %DUAL-5-NBRCHANGE: EIGRP-IPv4 10: Neighbor 10.160.0.5 (GigabitEthernet4/0) is down: K-value mismatch
R4(config-router)#no metric weights 1 0 1 1 1 1
R4(config-router)#
*Feb  4 15:35:23.899: %DUAL-5-NBRCHANGE: EIGRP-IPv4 10: Neighbor 10.160.0.5 (GigabitEthernet4/0) is up: new adjacency
```

### Where does EIGRP store all of its routes?
	
In the EIGRP topology table.

```
show ip eigrp topology
```

## EIGRP Update Process

* When neighbors come up, the routers exchange full topology tables with each other.
* When full topology table exchange is done, there is NO periodic re-flooding of topology table data.
* If something changes, only a partial update is sent about the prefix that was affected with a network change.

If I send you an update for a network, if nothing changes, there's no need for the routers to say something.

* Any change requires an update, the change can even be as small as a metric value change for a prefix, or it can be something like a full link failure.
* If a neighbor fails and the recovers, the full topology table is exchanged with it again.

EIGRP uses RTP (Reliable Transport Protocol) to send updates and ACK messages. It sits right behind the IP header and doesn't utilize TCP or UDP.

On multi-access EIGRP, typically send update messages to the multicast address 224.0.0.10 and expect unicast EIGRP ACK message from each neighbor. If you don't receive the ACK back, you'll send another single unicast Update to the same router, and you'll keep trying until the retry limit is exceeded. It will send 16 times before the neighbor relationship is finally killed.

### EIGRP Debugs and Commands

```
R4#debug eigrp ?
  address-family  EIGRP address-family debug commands
  fsm             EIGRP Dual Finite State Machine events/actions
  neighbors       EIGRP neighbors
  nsf             EIGRP Non-Stop Forwarding events/actions
  packets         EIGRP packets
  service-family  EIGRP service-family debug commands
  timers          EIGRP Timer Events
  transmit        EIGRP transmission events
```

```
R4(config)#router eigrp 10
R4(config-router)# ?
Router configuration commands:
  address-family       Enter Address Family command mode
  auto-summary         Enable automatic network number summarization
  bfd                  BFD configuration commands
  default              Set a command to its defaults
  default-information  Control distribution of default information
  default-metric       Set metric of redistributed routes
  distance             Define an administrative distance
  distribute-list      Filter entries in eigrp updates
  eigrp                EIGRP specific commands
  exit                 Exit from routing protocol configuration mode
  maximum-paths        Forward packets over multiple paths
  metric               Modify metrics and parameters for advertisement
  neighbor             Specify a neighbor router
  network              Enable routing on an IP network
  no                   Negate a command or set its defaults
  offset-list          Add or subtract offset from EIGRP metrics
  passive-interface    Suppress routing updates on an interface
  redistribute         Redistribute IPv4 routes from another routing protocol
  shutdown             Shutdown this instance of EIGRP 
  summary-metric       Specify summary to apply metric/filtering
  timers               Adjust routing timers
  traffic-share        How to compute traffic share over alternate paths
  variance             Control load balancing variance
```

```
R4#debug ip eigrp ?
  <1-65535>      Autonomous System
  A.B.C.D        IPv4 Address
  neighbor       Neighbor debugging
  notifications  Event notifications
  summary        Summary Route Processing
  vrf            Select a VPN Routing/Forwarding instance
  <cr>
```

```
R4#debug eigrp packet ?  
  SIAquery  EIGRP SIA-Query packets
  SIAreply  EIGRP SIA-Reply packets
  ack       EIGRP ack packets
  hello     EIGRP hello packets
  ipxsap    EIGRP ipxsap packets
  query     EIGRP query packets
  reply     EIGRP reply packets
  request   EIGRP request packets
  retry     EIGRP retransmissions
  stub      EIGRP stub packets
  terse     Display all EIGRP packets except Hellos
  update    EIGRP update packets
  verbose   Display all EIGRP packets
  <cr>

```