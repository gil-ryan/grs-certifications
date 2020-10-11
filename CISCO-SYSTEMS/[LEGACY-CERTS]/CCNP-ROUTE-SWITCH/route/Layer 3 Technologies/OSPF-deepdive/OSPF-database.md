# OSPF Database Description Packets

* OSPF Packet Types (Revisited):
    + Type 1: Hello
    + Type 2: Database Description (DBD)
    + Type 3: Link-State Request (LSR)
    + Type 4: Link-State Update (LSU)
    + Type 5: Link-State Acknowledgement (LSAck)

* Commmunicates
    + Link Maximum Transmission Unit (MTU)
    + Option (same options communcicated in Hello packets)
    + DBD bits
        - R-bit: OOBResynce (RFC 4811)
        - I-bit: Init bit
        - M-bit: More bit
        - MS-bit: Master bit
    + DBD Sequence Number
    + Link State Advertisment (LSA) Headers

## OSPF Database Description Packets

* Node with the higher Router ID (RID) becomes master
* Only master can increment DBD sequence number
* Key DBD bit value pairs:

|I|M| Packet Info|
|:-:|:-:|:-:|
|1|0| Single DBD packet describes all of the LSA headers|
|1|1| Series of DBD packets are neede for all LSA headers|
|0|0| LAST DBD Packet|
|0|1| set in the DBD packets that are between first and last DBD packets|

* The master is not necesaarily the Designated Router (DR)
* LSAck packet is not used as acknowledgement for DBD packets

### Configuration & Verification Commands

* Verification:
    + show ip int [int]
    + debug ip ospf adj

## OSPF Link State Request Packets

* OSPF Packet types are the same as above
* Communicates
    + Type of LSA being requested
    + The link-state ID of the LSA
    + The OSPF Router ID (RID) of the router responsible for originating the LSA
