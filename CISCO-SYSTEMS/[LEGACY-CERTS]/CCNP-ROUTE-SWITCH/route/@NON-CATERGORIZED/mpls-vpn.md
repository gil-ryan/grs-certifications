# Routing Across MPLS VPNs

## Branch Offices

Originally, branches were connected with leased lines. Eventually, ISPs offered L2 VPN for point-to-point connectivity, usint ATM or Frame Relay. Customers build their own L3 networks, and as a result, separate networks exist between the two types of traffic.

## MPLS

MPLS is a transport mechanism that is developed to carry data over a packet-switches network. It was designed to provide a high level of flexibility to operate seamlessly with any L2 or L3 technology. MPLS VPN is a sevice extension of the MPLS that is intended to provide VPN service that enables ISPs and large enterprises to build flexible, scalable, and secure VPNs. Two types of MPLS VPNs have been developed: 

* L2 MPLS VPN
* L3 MPLS VPN

### L2 MPLS VPN

The Layer 2 MPLS VPN backbone solution is providing the Layer 2 service across the backbone, where R1 and R2 are connected together directly using the same IP subnet. If you deploy a routing protocol over the Layer 2 MPLS VPN, neighbor adjacency is established between your R1 and R2 routers. The figure shows the connectivity through the backbone, which is illustrated as one switch.

### L3 MPLS VPN

The Layer 3 MPLS VPN backbone solution is providing the Layer 3 service across the backbone, where R1 and R2 are connected to ISP edge routers. A separate IP subnet is used on each side. If you deploy a routing protocol over this VPN, service providers need to participate in it. Neighbor adjacency is established between your R1 and ISP router and between your R2 and closest service PE router. The figure shows the connectivity through the backbone, which can be illustrated as one router.

From the customer perspective, selecting the Layer 3 or Layer 2 MPLS VPN will largely depend on customer requirements:

* Layer 2 MPLS VPN is useful for customers who run their own Layer 3 infrastructure and require Layer 2 connectivity from the service provider. In this case, the customer manages its own routing information.

* Layer 3 MPLS VPN is appropriate for customers who prefer to outsource their routing to a service provider. The service provider maintains and manages routing for the customer sites.
