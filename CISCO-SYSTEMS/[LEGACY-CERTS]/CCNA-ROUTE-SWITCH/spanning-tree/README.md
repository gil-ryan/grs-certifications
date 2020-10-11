# Spanning-Tree and Loop Prevention

The future of networking is a place where Spanning-Tree and switching loops don't exist.

## Overview

* Bridging loops
* Rapid Spanning-Tree
* Trees as related to RSTP

## Electing Root Bridge

- [ ] Elect one Root Bridge
- [ ] Elect one Root Port per bridge
- [ ] Elect designated Ports
- [ ] Block non-designated ports

* STP elects a root bridge for each VLAN, which means there can be different paths for traffic to flow toward.
* STP uses the 8-byte Bridge ID (BID), which is a combination of:
    * A 2-byte bridge priority + the 6-byte-system-id (MAC address) + VLAN # to divide on the root bridge and the root bridge path if there are multiple redundant links
    * The default STP bridge priority is 32768 <0-61440> and is decremented or incremented by 4096 to determine who is more preferred. The lower the priority the more preferred the bridge. This also comes into play when determining which ports go into blocking or discarding state to stop the loop.

## BID & BPDU

* The BPDU contains the following information:
    1. Root BID (the BID of the expected RB)
    2. Sender's BID
    3. Sender root cost (total cost to the RB)
    4. Timer values on the root switch (hello, MaxAge, forward delay)
* If the BID lists the root's BID as better, this is called a superior hello
* If the BID lists the root's BID as worse, this is called an inferior hello
