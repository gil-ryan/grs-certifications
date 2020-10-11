# UDP Vulnerabilities

_UDP_ is connectionless transport-layer protocol (L4?) that provides an interface between IP and upper-layer processes. _UDP_ protocol ports distinguish multiple applications running on a single device from one another. 

* No reliability
* No flow-control
* No error-recovery

_UDP_ is more simplistic

* Less bytes
* Less network overhead

The _UDP_ segment's header contains only source and destination port numbers, a _UDP_ checksum, and the segment length.

_UDP_ transport is known for several well-known protocols:

* NFS
* SNMP
* DNS
* TFTP
* Real-Time services, online games, streaming media, and VoIP

_UDP_ is vulnerable to checksum, which is an optional field, but more common these days.Checksum detects transmission errors, but is easy to recompute for attackers who want to alter application data.

There is no algorithm for _UDP_ to verify the sending packet source. An attacker can eavesdrop and make up false _UDP_ packets while spoofing the source. As long as the attacker knows the format of the messages that are sent and that the messages are not encrypted, they can exploit services, such as SNMPv1 and DNS.

Most attacks on _UDP_ relate to exhaustion of a shared resource, like buffers, link capacity, etc. Other exploits would be of bugs in protocol implementation, causing system crashes or insecure behavior. They all fall into the broad category of DoS attacks. As opposed  to TCP flooding, _UDP_ flooding with cause system resource starvation by send many _UDP_ packets to random ports on a victim's system. The victim's system wil notice that no application is listening on that port and reply with an _ICMP destination port unreachable packet_.