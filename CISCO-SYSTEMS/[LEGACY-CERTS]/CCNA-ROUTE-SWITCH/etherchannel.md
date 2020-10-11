# Etherchannel

```
!
interface Port-channel2
 description L2 PORT-CHANNEL TO DIST-A INT GIG3/2 - 3
 switchport access vlan 100
 switchport trunk encapsulation dot1q
 switchport mode trunk
end
```

```
DIST-B#sho etherchannel summary
Flags:  D - down        P - bundled in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      N - not in use, no aggregation
        f - failed to allocate aggregator

        M - not in use, minimum links not met
        m - not in use, port not aggregated due to minimum links not met
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port

        A - formed by Auto LAG


Number of channel-groups in use: 1
Number of aggregators:           1

Group  Port-channel  Protocol    Ports
------+-------------+-----------+-----------------------------------------------
2      Po2(SU)         LACP      Gi3/2(P)    Gi3/3(P)    
```

```
R2#sho run int port-channel 1
Building configuration...

Current configuration : 139 bytes
!
interface Port-channel1
 description L3 PORT-CHANNEL TO R1 INT GIG5/0 & 6/0
 ip address 10.1.2.2 255.255.255.240
 hold-queue 150 in
end
```