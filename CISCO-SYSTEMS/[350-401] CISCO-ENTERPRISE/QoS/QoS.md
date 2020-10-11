# Introduction to QoS

* What is QoS and why do we need it?
* QoS IOS-based tools
* Classifications and Markings
* Congestion Management Overview
* Queuing
* Congestion Avoidance

## Lab Infrastructure and Tools

* Wirehsark
* Onstinato (generate traffic)
* Laptop has static routes

## QoS Overivew

Why do we need Quality of Service?

* Predictable management of network resources during times of congestion


## MQC

* MQC = Modular QoS Command-Line
* Allow QoS features that apply Classification, Policing, etc to be configured independently and then linked-together as needed.
* Utilizes "Class-Maps", "Policy-Maps" and "Service-Policies",


* Classification done use "Class Maps"

```
class-map match-all Prec3
match ip precedence 3
class-map match-all Telnet
match access-group 101
```

* QoS actions for those classes defined in "Policy-Maps"

```
policy-map To-ISP
class Prec3
bandwidth percent 30
policy-map To-Core
class Prec3
set dscp af33
```

* QoS actions applied to interfaces using "Service-Policy".

```
policy-map To-ISP
class Prec3
police 5000000 exceed-action drop
policy-map To-Core
class Prec3
set dscp af33
!
Interface Serial0/1
    service-policy out To-ISP
!
Interface GigabitEthernet1
    service-policy out To-Core
```