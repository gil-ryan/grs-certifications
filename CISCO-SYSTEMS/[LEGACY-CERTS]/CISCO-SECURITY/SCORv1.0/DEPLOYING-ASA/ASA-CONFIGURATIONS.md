# Configuring ASA

## Transparent Mode

```
ASA# conf t
ASA(config)# firewall transparent
ASA(config)# interface Ethernet 0/0
ASA(config)# !
ASA(config-if)# switchport access vlan 10
ASA(config-if)# no shutdown
ASA(config-if)#  !
ASA(config-if)# interface Ethernet 0/1
ASA(config-if)# switchport access vlan 20
ASA(config-if)# no shutdown
ASA(config-if)#  !
ASA(config-if)# interface vlan 10
ASA(config-if)# nameif outside
INFO: Security level for “outside” set to 0 by default.
ASA(config-if)# bridge-group 1
ASA(config-if)#  !
ASA(config-if)# interface vlan 20
ASA(config-if)# nameif inside
INFO: Security level for “inside” set to 100 by default.
ASA(config-if)# bridge-group 1
ASA(config-if)#  !
ASA(config-if)# interface bvi 1
ASA(config-if)# ip address 192.168.0.10
```