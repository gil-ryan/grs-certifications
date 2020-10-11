# Dynamic Trunking Protocol

Cisco proprietary feature for automatically negotiating trunks.

## Modes

* Auto
* On
* Desirable

Desirable initiates the trunk, whereas Auto responds only.


| Mode | Trunk | Dynamic Desirable | Dynamic Auto | Access|
|:-:|:-:|:-:|:-:|:-:|
|trunk|yes|yes|yes|no|
|dynamic desirable|yes|yes|yes|no|
|dynamic auto|yes|yes|no|no|
|access|no|no|no|no|

## Configure

```
switchport mode dynamic [desirable|auto]
```

## Disable

```
switchport nonegotiate
```

## Verification

```
show interface trunk
show interface <interface> switchport
```
