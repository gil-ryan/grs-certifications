# EIGRP

## Neighborship Parameters & Overview

Requirements for routers to become EIGRP neighbors:

* Must be able to receive/send IP packets
* interface primary IP address must be in same subnet
    + Not neccesarily true:
![Primary-Secondary Neighbor Discovery](https://github.com/gil-ryan/grs-networking-private/blob/master/img/neighbors-wouldnt-init-discover.PNG)

Basically - as long as a primary on one, matches a secondary on another, we're all good.

### Example Configuration

#### R5

```
R5(config-if)#int gig4/0
R5(config-if)#no shut
R5(config-if)#ip add 10.160.0.5 255.255.255.0
R5(config-if)#ip add 10.160.1.5 255.255.255.0 secondary 
R5# 
*Feb  4 15:22:51.831: %DUAL-5-NBRCHANGE: EIGRP-IPv4 10: Neighbor 10.160.1.4 (GigabitEthernet4/0) is up: new adjacency
```

#### R4

```
R4(config)#int gig4/0
R4(config-if)#ip add 10.160.1.4 255.255.255.0
*Feb  4 15:20:51.655: %DUAL-6-NBRINFO: EIGRP-IPv4 10: Neighbor 10.160.0.5 (GigabitEthernet4/0) is blocked: not on common subnet (10.160.1.4/24)
R4(config-if)#ip add 10.160.0.4 255.255.255.0 secondary    
R4(config-if)#
*Feb  4 15:21:52.651: %DUAL-5-NBRCHANGE: EIGRP-IPv4 10: Neighbor 10.160.0.5 (GigabitEthernet4/0) is up: new adjacency
```

* AS number must be the same
    + If AS systems don't match, a debug won't even pick it up. So make sure to look out for that.
* connected interface must not be passive
* authentication must match
* k-values used must match

### 

## EIGRP Hello Packets, Hello & Dead Intervals/Timers

* by default, if a router does not receive a hello packet from its neighbor for 3 x hello-interval of period, it is considered dead.
* this time is known as a __dead (or Hold) interval__.
* The deafult dead interval for LAN is 15 seconds
* the deafult dead interval for T1 or slower bandwidth is 180 seconds
* it can be manually modified using the command:

```
ip hold-time eigrp <as><interval>
```

* The hello interval is for local devices - that is, how often to send hello packets out a certain interface.
* The head interval tells the "neighbor" device when to consider this local device dead.
* Hello and dead intervals also affect the network convergence.
* To verify hello and dead intervals:

```    
show ip eigrp interface detail x/x
show ip eigrp neighbors
```

## Authentication

* Must Pass for neighbor to establish
* Routers should use the same pre-shared key
* EIGRP only supports MD5 type authentication, not clear text
* Authentication helps prevent denial of service attackes (DoS)
* The authentication key is used to generate message digests for EIGRP packets
* These packets can still be intercepted by man-in-the-middle attacks

### Configuration Steps

* Key-chain Configuration
    + Key & Key-String
* Interface
        + Turn MD5 Auth
    + Associate Key String

```
R5(config)# key chain grs
R5(config)# key 1
R5(config)# key-string grs

R5(config-if)# int gig4/0
R5(config-if)# ip authentication mode eigrp 1 md5 
R5(config-if)# ip authentication key-chain eigrp 1 grs
```

* Key chain can also be configured to use time-based logic
* Multiple keys can be configured for different time periods
* if there are multiple active/valid keyse at the same time, the lowest key number value is used to generate the MD5 digest to send EIGRP packets

Every outbound EIGRP creates a hash based on the first available key. Now for packets that come in with an inbound hash, the router will check if the first key matches, if it doesn't, it moves to the next key.

### Timed Authentication in EIGRP

Naturally you have to have _NTP_ setup and synchronized amongst both routesr.

```
R5(config-keychain-key)#send-lifetime ?
  hh:mm:ss  Time to start
  local     Specify time in local timezone

R5(config-keychain-key)#acc           
R5(config-keychain-key)#accept-lifetime ?
  hh:mm:ss  Time to start
  local     Specify time in local timezone

R5#show key chain 
Key-chain grs:
    key 1 -- text "grs"
        accept lifetime (always valid) - (always valid) [valid now]
        send lifetime (always valid) - (always valid) [valid now]
```

```
R5(config-keychain-key)#accept-lifetime ?
  hh:mm:ss  Time to start
  local     Specify time in local timezone

R5(config-keychain-key)#accept-lifetime 01?
hh:mm:ss  

R5(config-keychain-key)#accept-lifetime local ?
  hh:mm:ss  Time to start

R5(config-keychain-key)#accept-lifetime local 12:21:00 ?
  <1-31>  Day of the month to start
  MONTH   Month of the year to start

R5(config-keychain-key)#accept-lifetime local 12:21:00 1 feb  ?
  <1993-2035>  Year to start

R5(config-keychain-key)#accept-lifetime local 12:21:00 1 feb  2036
                                                                 ^
% Invalid input detected at '^' marker.

R5(config-keychain-key)#
```

## EIGRP Passive Interface & More K-Values

There are two ways to advertise a subnet into EIGRP:

* using the __network__ command
* using __redistributed connected__

We can use passive-interface to stop sending hello packets out of selected interfaces. When an interface is configured as a passive-interface, two things happen:

1. it does not send EIGRP messages
2. it ignores any EIGRP messages received

```
R5(config-router)#router eigrp 11 
R5(config-router)# passive-interface default
<...>
R5(config)#router eigrp 111
R5(config-router)#passive-interface gig0/0
```

You can selectively unpassive interfaces that you'd like when you enable it globally, which is generally recommended.

```
R5(config)#router eigrp 10
R5(config-router)#passive-interface gigabitEthernet 4/0
<...>
  Passive Interface(s):
    GigabitEthernet4/0
```

### EIGRP K-Value

Must match for neighbors to form, and are also known as 'Metric Weights'. There are 5 K-values in EIGRP: K1,K2,K3,K4,K5.

Here is the metric formula:

![Metric Formula](https://github.com/gil-ryan/grs-networking-private/blob/master/img/eigrp-metric-forumla.png)

By defaul, EIGRP uses bandwidth(K1) and delay(K3) in its  formula to calculate metric value. Kind of making load and reliability not a factor. 

## Static Neighbors and Router-ID

EIGRP can be configure statically if neccessary, you might do so for two reasons:

1. L2 Media doesn't support Broadcasts
2. To ensure that no other device can intercept the EIGRP packets sent to multicast addressses between devices.

When a static neighbor is configured, the router is no longer allowed to listen to multicast hello packets, they're both only allowed to listen to unicast, unless the statement is removed. So generally adding static neighbors is not an option. You'll lose all of your dynamic routes.

### Check for a neighbors status

```
R4(config)#do sho ip eigrp neigh detail 
EIGRP-IPv4 Neighbors for AS(10)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
1   159.0.0.1               Gi2/0                    14 05:33:01  840  5000  0  13
   Version 9.8/3.0, Retrans: 0, Retries: 0, Prefixes: 1
   Topology-ids from peer - 0 
EIGRP-IPv4 Neighbors for AS(11)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
0   10.4.1.1                Gi3/0                    14 00:07:46   26   156  0  23
   Static neighbor
   Version 20.0/2.0, Retrans: 0, Retries: 0
   Topology-ids from peer - 0 
```

### Router-ID

* Used for preventing External routing loops
* Preferable defined with loopback address. (Just like OSPF)
* A router injecting external EIGRP routes also defines its Router-ID as the originator
    + If it can't find a loopback it will used the highest IP on another interface
* If two neighbors have the same Router-ID they can still become neighbors
    + The routers will not accept external updates from others that have the same Router-ID.

```
R4(config-router)#eigrp router-id 1.1.1.1   
```

All these routing protocols have their own routing table, and once selected will inject the route into the routers routing table.

After reviewing, two directly connected routers with the same router-id will become neighbors, but will not exchange any routes at all. They will not accept external or internal routes, they will let routes pass through.

If there is a router in the middle, they will accept EIGRP internal routes from each other.

## EIGRP Neighbors over WAN

Some things to keep in mind. 

### EIGRP neighbors over MPLS L3VPN.

* Neighbors are formed with teh service provider's PE router.
* No neighbor relationship is required with all the remote offices/branches/sites or data center.

Service Providers use Virtual Routing & Forwarding (VRFs) to isolate traffic over an MPLS cloud. MPLS VPNs might connect New York to Boston and the neighborship will be built between the ISP router (PE) and customers router(CPE) first. So you're forming a routing relationship with the ISP, and the ISP does the rest.

### EIGRP Neighbors over Metro Ethernet

* EIGRP neighbors over Metro-E is like building a neigjbor over a LAN segment.
* Metro Ethernet configuration is irrelevent to us; we consider them as an Ethernet handoff and treat them as a multi-access LAN segment.

### EIGRP Neighbors over Frame Relay

* Frame Relay provides Layer 2 WAN service.
* Neighbors can be formed with any router that connects to the FR cloud.
* The port can be configurated as point-to-point or multipoint (sub or physcial)
* It can have dynamic or static neighbor discoveries, depending on the FR cloud.
* There is one significant problem with EIGRP neighbors over FR coud: _split horizon_
* A single physical interface from the router is connected to the FR cloud.

Frame-Relay will use a _PVC_ to connect between each other. A single physical connection, so in the below picture, Network C will never learn about downstream Network X.

![Split Horizon FR WAN issue](https://github.com/gil-ryan/grs-networking-private/blob/master/img/network-c-cannot-learn-netork-x.PNG)

#### Quick Fix

1. Turn off _split horizon_
    * It's original purpose is to prevent routing loops, and there's little chance of that here.

Lets say EIGRP learns 100 new networks, it has to package those updates and multicast them out of its interface. Lets say that it takes 10 packets because we can't fit them in a single update. So we're sending 10 packets to 5 neighbors with 100 new networks and wait for them to all acknowledge that. An Ethernet frame on that one interface will carry all of them.

Frame Relay cannot do that, it cannot give the update to more than one router. The router CPU will replicate and send a copy to both networks, or all of those network and may soak up bandwidth.

### 

## EIGRP Topology Table, Messages, and Update Process

* After forming a relationship, neighbors exchange topology tables where they make routing decisions to inject.
* Router must first add subnets into the local topology table first.
* EIGRP Topology table keeps basic information about each unique prefix, including prefix, prefix length, metric information, and a few other details.

### Seeding the Local Topology Table

* A router can add routes into its local topology table in two ways:
    + Prefixs of connected subnets that are matched using the __network__ command.
    + Prefixes that are redistributed

#### Topology Exchange Messages

To revisit exchange messages is crucial:

* Hello
* Update
* ACK (Acknowledgement)
* Query
* Reply

__Update__ and __ack__ are used for topology exchanges.

* Update messages contain the following:
    + Prefix
    + Prefix Length
    + Metric components: delay, bandwidth, load & reliability
    + Nonmetric components: MTU and hop count

An external EIGRP update contains an additiona __External Data__ field in wireshark, which tells you the originating router and additional information.

You may choose things that cause EIGRP to bounce. If I send you 1000 new routes, the memory size of that is quite large. If you configure summarization, the router will kill its neighbor relationship and the other router will delete those routes. The neighborship will come back and the networks will be summarized.

### Topology Exchange over a WAN

#### EIGRP Split Horizon Issues

* EIGRP, by defaultm has split-horizon enabled on Frame Relay multipoint interfaces.
* With split horizon, an update receieved on a certain interface cannot go out the same interface.

[Click here to continue on EIGRP!](https://github.com/gil-ryan/grs-networking-private/blob/master/routing-and-switching/ccnp/route/Layer%203%20Technologies/CCNP-EIGRP2.md)
