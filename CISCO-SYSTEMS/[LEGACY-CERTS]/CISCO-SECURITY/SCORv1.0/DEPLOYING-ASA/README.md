# Deploying Cisco ASA Firewall

The Cisco ASA supports two firewall modes:

* routed
* transparent

When powering on the ASA, the default mode with be routed and allows the ASA the participate in the network as a L3 device.

Routed mode is preferred, but requires additional steps and may be more challening in existing enviroments as IP addressing is required.

Transparent mode would be easier to deployment because the ASA acts as a L2 switch.

## Routed Mode

* destination networks must be in the routing table
* dynamic routing or static routing is available
    + static
    + RIP
        - v1
        - v2
    + OSPF
    + EIGRP
    + BGP
    
## Transparent Mode

The firewall acts as a 'bump in the wire' (BITW) and is not seen as a routed hop to connected devices. The ASA connects the same IP subnet on its inside and outside interfaces and performs secure transparent bridging between the two interface.

In this mode, the traffic forwarding is based on destination MAC address. Access controls - such as ACLs, AAA, Stateful Packet Inspection, and Application Inspection and Control (AIC) - are supported for unicast IPv4 and IPv6 traffic.

## Routed Firewall Outbound Data Flow

* When receiving traffic on interface, ASA forwards it based on destination IP address.
    + That means the ASA checks the routing table first to determin the exit interface, and then if configured, NAT.

### This is the exact process:

1. Internal host requests a web page from the server on the Internet
2. ASA receives packet, checks for an active session present in state table, if so, the packed is forwarded according to session details.
3. It not, ASA finds destination IP in L3 header, then checks routing table to determing egress interface.
4. ASA verifies that the packet is allowed according to its security policy.  Traffic from higher security level is allowed to a lower security level by default.
5. If NAT is configured for the pair of ingress and egress interfaces, the ASA translated the real (private) IP address, to the mapped (public) IP address, which is one of the public IP addresses available on the out interface subnet. NAT deploymend mode is also considered.
6. ASA checks routing table and finds next hop IP address to server on Internet, so packet goes through the outside interface.
7. The web server responds to the request, since the session is already established, the ASA allows the return traffic back. The egress interface is determined by the session entry in the connection table.
8. ASA performs untranslated NAT (UN-NAT) on the packet received, and the public IP address of the packet is translated back to the mapped private IP address for the internal host.
9. ASA appliances checks the routing table and finds next hop IP address for packet destinated to the real IP address of the internal host.
10. ASA forwards the packet to the interal host through the inside interface.

## Transparent Firewall Guidelines

A different approach than routed firewall mode, different rules may apply, but the concept is always the same. L2 connectivity is achieved using a "bridge group," which also supports the use of more than two interfaces in a transparent firewall mode. Like with any other firewall interface, access control between these interfaces is controlled, and all of the usual firewall checks are in place.

To implement the ASA in transparent mode, a few high-level configuration tasks must be completed.

### Initially

* Change the firewall mode of the ASA from routed to transparent.      
    + Changing the firewall mode clears the running config because some commands are not supported for both modes.
* A bridge group must be configured and an IP address should be specified. A bridge group is a logical grouping of interfaces in a transparent mode and multiple bridge groups can be configured.
    + The ASA uses bridging techniques to pass traffic between interfaces.  
    + In transparent mode, these bridge groups cannot communicate with each other and traffic must exit the ASA before it is routed by an external router back to another bridge group in the ASA.
    + Briding functions are separate for each group, a few functions are shared between all bridge groups such as syslog server or AAA configs.
    + You __must__ configure a bridge group even if the ASA has two interfaces, but you can include multiple interfaces per bridge group when neccesary.
    + If you assign more than 2 interfaces per bridge group, you can control communication between multiple segments on the same network
        - for example: not just between inside and outside
        - you can also customize access rules between interfaces to allow only as much as desired.
* Interfaces that will be used on the Cisco ASA appliance must be assigned to the bridge groups available in the configuration. Each bridge group includes a Bridge Virtual Interface (BVI) to which you assign an IP address on the network. An IP address for the BVI is required for each bridge group for both management traffic and for traffice to pass through the ASA.
* If the ASA supports a dedicated management interface, you can also enable management interface for management trafic.

#### Bridge groups are allso supported in routed firewall mode

### Features not supported in Transparent Mode

* DHCP Relay
* DDNS
* Dynamic Routing
* Multicast routing
* QoS
* VPN termination
* DHCPv6
* Cisco Unified Communications

Opposite to routed mode, transparent mode behaves like a switch and fowards packets based on the destination MAC address of the packets. If the destination MAC is avaiable in the MAC address table, the ASA will send it out of the outside interface. Otherwise, it will ARP first to learn the MAC address of the next hop device.

### This is the exact process:

## ADD TRANSPARENT MODE STEPS HERE

## Cisco ASA Appliance Multiple Context Mode Overview

When you must implement different security policies for traffic from different customers or departments, you can use the virtualization feature supported on the  ASA.

