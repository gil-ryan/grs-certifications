# CCNP Route - EIGRP Lab

![EIGRP Lab/Topology](./ccnp-route-eigrp-lab.png)

## EIGRP Lab-1: Initial Configuration

Theory will be discussed of the workflows in this workbook. We have a literal [configuration file](./EIGRP.conf) in this directory that could be referenced.

### Verification

```
show ip eigrp neighbor
show ip eigrp topology
show ip route eigrp
```

## EIGRP Lab-2: EIGRP Neighborships

When EIGRP neighbors are configured, they must be in the same Autonomous System. This lab reviews the syslogs and debugs for errors that arise when the EIGRP AS numbers are mismatched.

When EIGRP neighbors are mismatched, there is no indication of the problem in any syslogs, and debugs will not display any errors either.

### Task 1

After reviewing mismatched EIGRP instances, reconfigure the Routers and restore services. Next, configure  Router 2's __1.2.1.x__ interface with primary and seconday IP addresses as [needed](./EIGRP.CONF).

The interface configuration will have the console output saying this every couple of moments:

```
*Nov  1 01:50:09.047: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 2.4.2.4 (FastEthernet1/0) is up: new adjacency
```

On Router-2, it is receiving EIGRP Hello packets sourced from the primary IP address on Router-1 (1.2.1.1). Router-2 does have this subnet configure, but its interface is a __secondary__ subnet. So it attempts to send an EIGRP _Update packet_ to Router-1.

Router-1 is blocking Router-2 because the source IP address of Router-2's EIGRP Hello's (2.2.2.2) is not from a subnet that Router-1 recognizes. When Router-1 is receiving the EIGRP _Update_ from Router-2, it discards it.

Since EIGRP is reliable, Router-2 will attempt to resent the EIGRP _Update Packets_ to Router-1 several times before giving up.

Next - we'll take a look at the output of a *__show ip protocols__*:

```
L2R2#sho ip protocols
*** IP Routing is NSF aware ***

Routing Protocol is "eigrp 200"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Default networks flagged in outgoing updates
  Default networks accepted from incoming updates
  EIGRP-IPv4 Protocol for AS(200)
    Metric weight K1=1, K2=0, K3=1, K4=0, K5=0
    NSF-aware route hold timer is 240
    Router-ID: 2.4.2.2
    Topology : 0 (base) 
      Active Timer: 3 min
      Distance: internal 90 external 170
      Maximum path: 4
      Maximum hopcount 100
      Maximum metric variance 1

  Automatic Summarization: disabled
  Maximum path: 4
  Routing for Networks:
  Routing Information Sources:
    Gateway         Distance      Last Update
  Distance: internal 90 external 170

Routing Protocol is "eigrp 100"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Default networks flagged in outgoing updates
  Default networks accepted from incoming updates
  EIGRP-IPv4 Protocol for AS(100)
    Metric weight K1=1, K2=0, K3=1, K4=0, K5=0
    NSF-aware route hold timer is 240
    Router-ID: 2.4.2.2
    Topology : 0 (base) 
      Active Timer: 3 min
      Distance: internal 90 external 170
      Maximum path: 4
      Maximum hopcount 100
      Maximum metric variance 1

  Automatic Summarization: disabled
  Maximum path: 4
  Routing for Networks:
    1.2.1.0/24
    2.4.2.0/24
  Routing Information Sources:
    Gateway         Distance      Last Update
    2.4.2.4               90      00:00:09
    1.2.1.1               90      00:00:09
  Distance: internal 90 external 170
```

#### Notice the EIGRP metric weight

### Task 1 (continued)

Hopefully after runnig this command, you should still have the default EIGRP metric k-value weights:

```
Metric weight K1=1, K2=0, K3=1, K4=0, K5=0
```

So lets take a quick look at what happens when EIGRP metric weights are mismatched. Again, the terminal will scream at you:

```
*Nov  1 02:10:10.591: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 2.4.2.4 (FastEthernet1/0) is down: metric changed
L2R2(config-router)#
*Nov  1 02:10:12.275: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 1.2.1.1 (FastEthernet1/1) is down: K-value mismatch
L2R2(config-router)#
```