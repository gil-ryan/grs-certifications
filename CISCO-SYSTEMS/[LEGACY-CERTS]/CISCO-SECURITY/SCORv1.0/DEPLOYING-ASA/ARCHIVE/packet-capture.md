# Packet Captures on Cisco ASA

Here is a sample configuration to collect a packet capture. This example collects traffic matching IP address _10.1.0.25_ coming through the inside interface to any destination and going egress through the outside interface.

```
! inside
capture asdm_cap_ingress match ip 10.1.0.25 255.255.255.255 0.0.0. 0.0.0.0
capture asdm_cap_ingress packet-length 1522 buffer 524288
capture asdm_cap_ingress interface inside

! outside
capture asdm_cap_egress match ip 10.1.0.25 255.255.255.255 0.0.0.0 0.0.0.0
capture asdm_cap_egress packet-length 1522 buffer 524288
capture asdm_cap_ingress interface outside
```

