# Border Gateway Protocol - BGP

You will use the _router bgp_ command to start the __BGP__ routing process in the router. There can be, at most, one __BGP__ process in a router. It must be assigned the local AS number.

> router(config)# router bgp as-number

## AS Numbers

The AS number can be public or private. Public AS numbers range from 1 to 64,511 or 131,072 to 4,199,999,999. An Internet registry (ARIN: http://www.arin.net or RIPE: http://www.ripe.net) assigns public AS numbers.

* Public
  + 1 to 64,511
  + 31,072 to 4,199,999,999
* Private
  + 64,512 to 65,534
  + 4,200,000,000 to 4,294,967,294

## External Neighbors

Review the following command snippet and their respective descriptions underneath:

```
router(config-router)# neighbor ip-address _remote-as as-number_
router(config-router)# neighbor ip-address description _neighbor description_
router(config-router)# neighbor ip-address shutdown
```

* Defines an external neighbor
  +External neighbor has to be reachable over directly connected subnet
* Assigns a description to an external neighbor
* Disables a __BGP__ neighbor

## Announcing networks in BGP

Before injecting any local routing information into the __BGP__ table for advertising to other __BGP__ routers, some basic configuration is needed.

Only administratively defined networks are announced in __BGP__

* manually configure network to be announced
* use redistribtion from IGP
* use aggregation to announce summary prefixes

There are two ways to do so:

* Use the __network__ configuration command to list network candidates. If the network is reachable by the local router (in its route table) then the network is injected into the __BGP__ table.
* Redistributing the route information that is learned from other routing protocols into the __BGP__ table. You can use the IGP whithin the AS, so any route learned from the local IGP can be injected by using route redistribution.

