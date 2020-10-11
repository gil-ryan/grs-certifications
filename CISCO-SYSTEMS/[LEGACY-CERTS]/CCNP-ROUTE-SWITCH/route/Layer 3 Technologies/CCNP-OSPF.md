# CCNP OSPF

Review some materials from the [CCNA](https://github.com/gil-ryan/grs-networking-private/blob/master/routing-and-switching/ccna/route/Layer%203%20Technologies/CCNA-OSPF.md)!

## Introduction to OSPF

* OSPF is a link state routing protocol

## OSPF Neighbor Discovery, Hello Packet Parameters, Hello & Dead Intervals

### OSPF Neighbor Discovery on a LAN

* Unlike EIGRP, OSPF neighborship is more complex.
* To become neighbors, the concept is the same: Send Hello packets and match certain parameters; if they match, become neighbor
* OSPF has two neighborship classes:
    + 2-Way Neighbors
    + Fully adjacent neighbors
* OSPF uses an underlying finite state machine (FSM) with eight neighbor states that describe the current state of each OSPF neighbor.
* The difference between the two classes:
    + 2-Way Neighbor - OSPF is in the two-way state
    + Fully Adjacent Neighbor - OSPF is in the full state
* OSPF FSM neighbor states will be discussed later.

## 

* OSPF sends multicast OSPF hello messages on LAN when the following two requirements are met:
    + OSPF has been enabled on the interface, using either the __network__ router ospf subcommand or the __ip ospf area__ interface-level subcommand
    + The interface has not been made passive using the __passive-interface__ router ospf subcommand.

## 

* Hello messages are sent using multicast 224.0.0.5
* Passive interface uses the same concept discussed in EIGRP; that is, no Hello messages are sent on that interface but that subnet is still advertised into OSPF.
* Hello messages also include several parameters; out of those, a few must match and the other are just for information

## Hello Message Parameters

*  __ip ospf__ <process-id> __area__ <area-number> is an alternative to __network__ command
* In Hello message/packet, the following parameters must match:
    + Hello Interval
    + Dead Interval
    + Area ID
    + Subnet mask
    + Stub area flag
    + Authentication

### 

* Additional parameters that may be present in the Hello packet depending on network conditions are:
    + OSPF router ID
    + List of neighbors reachable on the interface
    + Router priority
    + Designated router (DR) IP address
    + Backup DR IP address
* OSPF Router ID is the parameter that can cause problems

### 

* Like EIGRP, OSPF also uses the periodic Hello packets. The period interval at which Hello packets are sent is known as the __Hello Interval__.
* If a Hello is not received from a particular neighbor, that neighbor for a period of time it is considered down.
* This is know as the __Dead Interval__.
* By default 4x (Hello Inteval)

### 

* On a LAN, the default value of the Hello Interval is 10 seconds, and the dead interval is 40 seconds.
* If the Hello interval on a certain interface is changed, the dead interval is automatically changed to 4 x the hello.
* When changing the hello interval on a certain interface, the other side must also change its hello interval, otherwise the neighborship will go down.

### 

* To change hello and dead intervals on an interface, use the following commands:
    + __ip ospf hello-interval__ <value>
    + __ip ospf dead-interval__ <value>
* To verify these intervals:
    + __show ip ospf interface__ <interface>

### 

* Hello and dead intervals can be modified to have faster convergence
* With OSPF, if a neighbor is down it can be discovered in a second, that is, the hello interval can be changed to subsecond value and the dead interval can be made 1 second
    + __ip ospf dead-interval minimal hello-multiplier__ multiplier
* BFD can also be used for fast convergence

## OSPF Router-ID & MTU Mismatch

* Just like EIGRP, OSPF has a router-ID associated with the OSPF process.
* Each router should have a unique router-ID
* If two routers have the same OSPF router-ID, OSPF prevents neighborship between them.
* Other router(s) who form neighbors with routers using the same OSPF router-ID have a problem with the topology flooding process.

## OSPF Router-ID

* To change the OSPF router-id, use the following command:
    + __router-id__ x.x.x.x (OSPF Mode command)
* To verify the OSPF router-id:
    + __show ip ospf__
    + __show ip ospf database__
    + __show ip protocols__

