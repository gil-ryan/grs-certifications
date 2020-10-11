# Named EIGRP

```
router eigrp GRS-1
!
address-family ipv4 unicast autonomous-system 10
!
topology base
exit-af-topology
exit-address-family
```

```
router eigrp GRS-1
address-family ipv4 autonomous-system 10
default af-interface 
default af-interface default
```