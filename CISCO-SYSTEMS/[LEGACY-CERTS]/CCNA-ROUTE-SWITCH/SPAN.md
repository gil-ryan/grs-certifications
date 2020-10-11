# SPAN and RSPAN

Switchport Analyzer (SPAN) and Remote SPAN (RSPAN) are generally used for network monitoring.

You can copy ingress traffic from a port or VLAN and send those frame copies to an egress port for observation.

## Switchport Analyzer

```
monitor session 1

```

## Remote SPAN

### Source Switch

1. 
```
vlan 300
remote-span
```

2. 

```
monitor session 1 source interface FastEthernet0/1
monitor session 1 destination remote vlan 100 reflector-port FastEthernet0/2
interface FastEthernet0/2
switchportmode trunk
```

### Destination Switch

3.

```
vlan 300
remote-span
```

4. 

```
monitor session 1 source remote vlan 300
interface FastEthernet0/3
switchport mode trunk
```

5. 

```
monitor session 1 destination interface FastEthernet0/4
```

## Verify RSPAN

```
show monitor session 1
```

```
SW1(config)#vlan 300
SW1(config-vlan)#remote
SW1(config-vlan)#remote-span 

SW1(config-if)#
*Mar  9 04:50:10.858: %PM-4-INACTIVE: putting Gi0/1 in inactive state because access VLAN is remote SPAN VLAN

```