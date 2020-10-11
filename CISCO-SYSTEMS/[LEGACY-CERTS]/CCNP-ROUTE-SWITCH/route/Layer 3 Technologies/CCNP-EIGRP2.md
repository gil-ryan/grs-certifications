# EIGRP Continued

[Want to start from the beginning?](https://github.com/gil-ryan/grs-networking-private/blob/master/routing-and-switching/ccnp/route/Layer%203%20Technologies/CCNP-EIGRP.md)

## EIGRP Topology Exchange over WAN

So say we have to disable _Split Horizon_ on any interface, use the following command:

```
R4(config-if)#no ip split-horizon eigrp 11
*Feb  5 12:39:54.767: %DUAL-5-NBRCHANGE: EIGRP-IPv4 11: Neighbor 10.160.0.5 (GigabitEthernet4/0) is resync: split horizon changed
R4(config-if)#no ip split-horizon eigrp 11
R4(config-if)#do sho ip int gig3/0
GigabitEthernet3/0 is up, line protocol is up
<...>
  Split horizon is enabled
<...>
```

Take note: Typing _no ip split-horizon_ by default will turn __Split Horizon__ off for _RIP_ specifically. It requires the eigrp and AS number appended to the command.

### EIGRP Percentage Bandwidth

* On NBMA media, EIGRP updates cannot be sent because the underlying Layer 2 media does not support multicast packets.
* Normally a single update is sent to 224.0.0.10.
* In this case, EIGRP must send a copy of update messages to each reachable neighbor.
* This can cause links to saturate with EIGRP traffic alone.
    + This requies A LOT of updates, like 50 update packets that are require to be copied 25 times.
* To prevent this, only 50% of interface bandwidth is reserved for EIGRP messages.

* This 50% is of the value configured with the __bandwidth__ command on that interface.
* This can be changed with the following interface level command:

> ip bandwidth-percent eigrp <asn> <percent>

```
R4(config-if)#int gig4/0
R4(config-if)#ip bandwidth-percent eigrp 10 25
```

* On P2P link, ensure that bandwidth is set to committed information rate (CIR) of the single PVC of this interface.

Committed information rate should match your access rate, but your provide wants the highest CIR so they could bill you the most. Your routing protocol, in this case EIGRP, doesn't know that.

* On multipoint interfaces, ensure that the bandwidth is the total of CIR of all VCs assigned on that interface.
    + Obviously cannot exceed the line rate of the physical interface.

![Crude drawing of calculating bandwidth for multipoint interface](https://github.com/gil-ryan/grs-networking-private/blob/master/img/crude-pvc-bandwidth-EIGRP.PNG)

#### EIGRP Percent Bandwidth Example

```
R4(config)#int ser6/0
R4(config-if)#encapsulation frame-relay
R4(config-if)#exit
Not all config may be removed and may reappear after reactivating the sub-interface
R4(config)#int ser6/0.1 multipoint
R4(config-subif)#ip add 10.1.1.1 255.255.255.0
R4(config-subif)#frame-relay interface-dlci 102
R4(config-fr-dlci)#frame-relay interface-dlci 103
R4(config-fr-dlci)#exit
R4(config-subif)#bandwidth 100
R4(config-subif)#ip bandwi
R4(config-subif)#ip bandwidth-percent eigrp 1 10
R4(config-subif)#do sho run int ser6/0.1
Building configuration...

Current configuration : 187 bytes
!
interface Serial6/0.1 multipoint
 bandwidth 100
 ip address 10.1.1.1 255.255.255.0
 ip bandwidth-percent eigrp 1 10
 frame-relay interface-dlci 102
 frame-relay interface-dlci 103
end

R4(config-subif)#
```

* In this example, the bandwidth configured is 100Kbps; normally EIGRP would consume up to 50% of this (50Kbps) divided between two VCs.
* However, we're allowed EIGRP a maximum of 10Kbps (10% of 100Kbps BW)...divided between two VCs this gives EIGRP for each VC a maximum of 5Kbps.

### EIGRP Feasible Distance & Reported Distance

* When a router has received all the updates messages from its neighbors and has populated the topology table, it must decide which prefixes to inject into the routing table.
* This decision depends on:
    + Feasible Distance (FD)
    + Reported Distance (RD)

__Feasible Distance__: From the local router's perspective, the metric value for a route, used by the local router, to choose the best route for that prefix.

__Reported Distance__: From the neighboring router's perspective (the neighbor that told the local router about the route), the metric value for a route that is used by the local router when converging to new routes (Also known as __Advertised Distance__)

Feasible distance is the total distance, the sum of delays plus slowest bandwidth. Now, to find both distances, use the following:

```
R4#show ip route eigrp
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is not set

      5.0.0.0/32 is subnetted, 1 subnets
D        5.5.5.5 [90/130816] via 10.160.0.5, 02:26:57, GigabitEthernet4/0
      10.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
D        10.0.0.0/24 [90/3072] via 159.0.0.1, 02:26:47, GigabitEthernet2/0
      55.0.0.0/32 is subnetted, 1 subnets
D        55.55.55.55 [90/130816] via 10.160.0.5, 02:26:57, GigabitEthernet4/0

R4#show ip eigrp topology 
EIGRP-IPv4 Topology Table for AS(10)/ID(44.44.44.44)
Codes: P - Passive, A - Active, U - Update, Q - Query, R - Reply,
       r - reply Status, s - sia Status 

P 44.44.44.44/32, 1 successors, FD is 128256
        via Connected, Loopback0
P 10.160.1.0/24, 1 successors, FD is 2816
        via Connected, GigabitEthernet4/0
P 4.4.4.4/32, 1 successors, FD is 128256
        via Connected, Loopback1
P 10.0.0.0/24, 1 successors, FD is 3072
        via 159.0.0.1 (3072/2816), GigabitEthernet2/0
P 10.160.0.0/24, 1 successors, FD is 2816
        via Connected, GigabitEthernet4/0
P 10.4.1.0/24, 1 successors, FD is 2816
        via Connected, GigabitEthernet3/0
P 159.0.0.0/24, 1 successors, FD is 2816
        via Connected, GigabitEthernet2/0

EIGRP-IPv4 Topology Table for AS(11)/ID(44.44.44.44)
Codes: P - Passive, A - Active, U - Update, Q - Query, R - Reply,
       r - reply Status, s - sia Status 

P 44.44.44.44/32, 1 successors, FD is 128256
        via Rconnected (128256/0)
P 55.55.55.55/32, 1 successors, FD is 130816
        via 10.160.0.5 (130816/128256), GigabitEthernet4/0
P 10.160.1.0/24, 1 successors, FD is 2816
        via Connected, GigabitEthernet4/0
P 4.4.4.4/32, 1 successors, FD is 128256
        via Rconnected (128256/0)
P 5.5.5.5/32, 1 successors, FD is 130816
        via 10.160.0.5 (130816/128256), GigabitEthernet4/0
P 10.160.0.0/24, 1 successors, FD is 2816
        via Connected, GigabitEthernet4/0
P 10.4.1.0/24, 1 successors, FD is 2816
        via Connected, GigabitEthernet3/0
P 159.0.0.0/24, 1 successors, FD is 2816
        via Rconnected (2816/0)
```

## EIGRP Metric Manipulation

How can we manipulate metrics to obtain path selection?

### Metric Tuning

* EIGRP metric can be manipulated to prefer one path over the other path.
* There are two ways to manipulate EIGRP metric value:
    + Changing bandwidth and/or delay values
    + Using offset-list

Offsets say you can use whatever distance you can come up with, then add a fixed amount. Changing the distance without manipulating the bandwidth or delay values. Most importantly, when changing bandwidth and/or delay values, remember that units of bandwidth are __kilobits/second__ and units of delay are __tens-of-microseconds__, used by EIGRP. 

* Changing bandwidth value is __not__ recommended.
* Changing bandwidth may also affect other interface-level things, such as QoS (queuing, shaping, etc.)
* It's best to change delay because bandwidth could affect OSPF, or another protocol you're running.

* On WAN subinterfaces, bandwidth value should be chosen carefully.
* Bandwidth on these interfaces should be the sum of all CIR
* It is recommended that you change the delay value at the interface level to manipulate EIGRP metric value for routes learned from that interface.

* When changing the bandwidth and/or delay values on certain interface, EIGRP metric value is manipulated for all the routes reachable out that interface
* Changing bandwidth or delay only affects FD, not the RD

* EIGRP distance calculation can be changed by configuring the k-value (weightings).

If your goal is to modify distance, offset lists are much easier.

* Offset-list simply allows a value to be added to the EIGRP metric.
* Offset-list is configured by using the following command:
    __offset-list__ {access-list-number | access-list-name} {in|out} <offset value> [interface-type interface-number]

If you're learning 200 routes and you modify an interface's bandwidth, you'll be affecting them all. This way you can select a specific prefix. You don't have to include an interface, but if it matches the ACL it will increase the metric and so on.

* The 'offset' value is added to both the FD and the RD.
* Prefixes/prefix lengths are matched using the ACL and offset value is only applied to the routes matched by the ACL permit clause.

When you first learn, you learn an ACL is paired with an access-group. It's really just a classification method, but it truly depends on what feature is utilizing that mechanism. Now if say, NAT is using the access-list, deny will mean just to not NAT it. In this case a deny means not to offest it.

* Direction of the update message is also specified in the command, in or out.
* If all routes are required to be matched to manipulate the EIGRP metric using offset-list, we have two ways to match them all.
    + By using an ACL with permit any
    + by using ACL number '0' in offset-list command

#### Example configuration:

```
R4(config-router)#do sho ip route eigrp  
<...>
      5.0.0.0/32 is subnetted, 1 subnets
D        5.5.5.5 [90/230816] via 10.160.0.5, 00:00:04, GigabitEthernet4/0

R4(config)#access-list 1 permit 5.5.5.5 0.0.0.0
R4(config)#router eigrp 11
R4(config-router)#offset-list 1 in 1000000
R4(config-router)#
*Feb  5 19:52:57.890: %DUAL-5-NBRCHANGE: EIGRP-IPv4 11: Neighbor 10.160.0.5 (GigabitEthernet4/0) is resync: route configuration changed
R4(config-router)#do sho ip eigrp topology
<...>
      5.0.0.0/32 is subnetted, 1 subnets
D        5.5.5.5 [90/1130816] via 10.160.0.5, 00:00:08, GigabitEthernet4/0
<...>
```

As you can see, setting an offset list will cause your neighbor relationship to bounce. Also - in this example, we're using a directly connected loopback interface as and setting the offset to 1M, the reported distance goes from 230816 to 1130816

## EIGRP Convergence

### Successor and Feasible Successor Route

* EIGRP can converge very quickly
* To understand EIGRP convergence, we must first understand the concept of succesor and feasible successors.
* When multiple possible routes exist, the route with the smallest feasible distance is elected as the primary route.

If two routers are offering a new route, and one offers the route via redistribute connected with a RD of 500, and the other router advertises with the RD of 1000, which will be preferred?

The answer would be the 2nd router with the higher RD. The reason being is that the AD of the first router will be higher because a _redistribute connected_ advertisement is an external route, and therefore it's administrative distance is 170, whereas the 2nd router's AD is 90, an internal route. The takeaway? Administrative distance will always be considered before the metric.

* The primary route is known as the successor route.
* For faster convergence, EIGRP must determine which non-successor routes can be used immediately if the successor route fails.
* Proactive Calculation for Immediate Failover
* The EIGRP router uses the reported distance (RD) value to calculate if a backup route is loop-free.
* The elected backup route(s) are called __Feasible Successor Routes__

Before I consider a feasible successor, I must consider if there's a potential routing loop. So how does EIGRP calculate the Feasible Successor?

* Evaluate  all Non-Successor Routes 

1. First the route has to not be in the routing table, otherwise it would be considered the successor route.
2. Look at the reported distance, is it better? Evaluated Route's RD < FD
3. Is there a routing loop.

The __Feasibility Condition__

First time hearing a route, looks into the topology table and RD is 500 from vector metrics. Calculating my own Feasible distance is 1000 and the routers advertise it out to my neighbors. Then one of my neighbors advertises the same route back to me, he'll advertise some reported distance or vector metrics. If this route is the same one I just advertised, if it's a loop it's going to have the same feasible distance or higher. The router will put it in it's topology table but will not consider it a feasible successor, the Feasible Distance must be lower.

EIGRP doesn't load balance. Here's an analogy, the yellow pages had entries created by say, AT&T. When you going to plumbing section and there are many selections, is it their job to tell you which one to call? No. EIGRP creates the routing table, now it's another processes job to load balance, in this case it's CEF.

I want to make a route not as appealing. Rather than change the interface's bandwidth, we can use an offset list. For a test, we can modify the delay on an interface that makes it's feasible distance worse than the reported distance.

show ip eigrp topology shows you only successors and feasible successors, if you want them all you need to add all-links.

show ip eigrp topology all-links

If I've learned about the same route from another router, and they don't meet the feasibility condition to be a feasible successor. What do we do?

### Converging by going Active

The code __P__ in the topology table stand for passive and is a good indication.

* The route for which a successor route exists and has not failed remains in the __passive state__.
* Successor Route fails, and no Feasible Successor Route
* EIGRP goes __active__ for that route.

When a route is active, the router is actively trying to recover it and find other neighbors that have a possible route there.

* The router begins the process of determing whether any loop-free alternative route exists.
* The output of __show ip eigrp topology__ will have P (passive) or A(active) in front of a certain route, based on its current state
* EIGRP sends a Query message to all its neighbors, except the neighbor from which the route failed.

* The neighbor that received the query message considers itself to have a loop-free route if that neighbor is passive for that prefix.
* If it does, the neighbor does not forward the Query message any further
* The neighbor sends an EIGRP Reply message, telling the original router that it has a loop-free route.

* If it doesn't, the neighbor sends a Query message to its downstream neighbors
* The neighbor does not send a Reply message to original router until it receives Reply from all its neighbors.
* Once all devices respond, the neighbor sends a Reply to the original router.

* Once the original router has received a Reply from all neighbors...
        + If new loop-free route(s) were learned, install the best as the sucessor route
        + If no route(s) are learned, the active prefix is removed from the topology table
* What happens if the original Router doesn't receive a Reply from all its neighbors?

What if a query goes way down the line, like five routers, and the buffer is full, the link is congested, or the packet is corrupted for some reason. 

* Active Timer = 3-minutes
        + When I send a query to my neighbor, that neighbor has 3 minutes. Which is a really long time, so there must be something wrong.
        + Even if you're corresponding with Hello packets, if you don't respond within that three minutes, you're considered stuck-in-active and our neighbor relationship will go down.

* By default, after ~90-seconds (minute and a half), the router will send a SIA-Query to the neighbor
        + If neighbor is still alive but waiting for a response to its own Query (sent further downstream), it will respond upsteam with SIA-Reply
        + If neighbor doesn't respon, active timer continues to decrement
        + If neighbor still doesn't respon by 3-minutes, declares Stuck-In-Active and relationship reset.

* You can configure this timer using the following command:

```
R4(config-router)#timers active-time ?
  <1-65535>  active state time limit in minutes
  disabled   disable time limit for active state
```

Recap, the successor is the router you're using as the next-hop to reach a destination prefix. You could have potentially more than one successor and they could all show up in the routing table.

### Limiting the Query Scope

* Stub Routers
* Route Summarization

A router that should not forward traffic between two remote EIGRP-learned subnets is know as a __stub router__. A stub router does not advertise EIGRP-learned routes from one neighbor to other EIGRP neighbors.

* Non-stub routers note which routers are stub routers, and they do not forward Query messages to the stub-routers.
* By default, when a router is configured as an EIGRP stub router, it only advertises:
        + Connected
        + Summary
* To configure a router as an EIGRP stub-router:

```
R4(config-router)#eigrp stub  ?
  connected      Do advertise connected routes
  leak-map       Allow dynamic prefixes based on the leak-map
  receive-only   Set receive only neighbor
  redistributed  Do advertise redistributed routes
  static         Do advertise static routes
  summary        Do advertise summary routes
  <cr>
```

## EIGRP Variance

## EIGRP Filtering

## EIGRP Summarization

## EIGRP Default Route
