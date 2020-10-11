# TCP/IP in Security

## Legacy TCP/IP

_TCP_ (RFC 793) developed under the sponsership of the U.S. DoD, was designed to work in a trusted enviroment. The model was developed as a flexible, fault-tolerant set of protocols that were robust enough to avoid failure ir one or more nodes went down.

For example. _TCP_/IP is shipped with Berkeley r-utilities, such as _rlogin_, remote copy (_rcp_), and remote command execution (_rsh_). These were designed as password-free access to Unix OS. 

Although there are some advantages to r-utilites, they should be avoided, because they are extremely insecure. These days, _TCP_/IP consists of four main protocols:

* IP
* _TCP_
* UDP
* ICMP

## IP Vulnerabilities

* The IP is a connectionless protocol that is mainly used to route information across the Internet.
* IP depends on upper-level _TCP_/IP suite layers to provide accountability and reliability.

Many programs for generating spoofed IP datagrams are available for free on the Internet, for example, __hping__ lets you prepare spoofed IP datagrams with a simple one-line command, and you can send them to almost anybody in the world. 

* Address Resolution Protocol (ARP) spoofing to link a MAC address to the IP address of a legitimate host on the network to divert the traffic that is intended for one station to someone else. 
* The Simple Mail Transfer Protocol (SMTP) is also a target for spoofing the email source because SMTP does not verify the sender's address, so you can send any email to anybody pretending to be someone else. As a result, no packet can be trusted, and each packet must earn its trust through the network’s ability to classify and enforce.

These are key and most current vulnerabilities:

* __Man-in-the-Middle (MITM)__
* __Session Hijacking__
* __IP Address Spoofing__
* __DoS Attacks__
* __Distributed Denial of Services (DDoS) Attacks__
* __Resource Exhaustion Attacks__

## ICMP Vulnerabilities

ICMP is also a connectionless proctcol that does not use any port number and works on the network layer. ICMP was not designed to transfer data the same way as _TCP_ and UDP.

* ICMP is for diagnostic measures.
* Routers and switches will transmit diagnostic information

### Major issues with ICMP

* Reconnaissance and scanning
* ICMP unreachables
* ICMP mask reply
* ICMP redirects
* ICMP router discovery
* Firewalk

* ICMP tunneling
* ICMP-based Operation System Fingerprinting

### DoS attacks w ICMP

* ICMP Flood attack
* Smurf attack
    + DDoS ICMP attack

## _TCP_ Vulnerabilities

_TCP_ segments reside within IP packets. The _TCP_ header appears immediately after the IP header and supplies information specific to the _TCP_ protocol. Over UDP, _TCP_ provides:

* more functionality
* higher overhead
* additional fields
    + provides reliability
    + provides flow control
    + stateful communication

### _TCP_ Three-Way Handshake

Before data is transferred, the three-way handshake must take place. At the conclusion, a four-way handshake takes place, where a graceful tear-down requires a pair of Finished (FIN) and Acknowledgement (ACK) segments from each _TCP_ endpoint. Example protocols that utilze _TCP_:

* HTTP
* Server Message Block
* Secure Sockets Layer/Transport Security Layer
* FTP
* Many more

### _TCP_ Vulnerability Examples

* _TCP_ SYN flooding
    + Can cause a DoS attack, _TCP_ connections that have been initiated but not finished are called half-open connections. This allows an attacker to spoof their address and leave the inital three-way handshake only two-parts done. Do this enough and it will abrupt service.
    + In this attack, the attacker has no interest in examining the responses from  the victim.
* _TCP_ session hijacking
    + oldest type of session hijacking
    + If attackers manage to predict the ISN, they can actually send the last ACK data packet to the server, spoofing as the original host, and then hijack the _TCP_ connection. 
    + Refer to three-way handshake engagement to further understand the complexity of this attack.
        - __Non-blind spoofing__: The attacker can see the traffic that is being sent between the host and the target. Non-blind spoofing is the easiest type of session hijacking to perform, but it requires attacker to capture packets as they are passing between the two machines. Spoofing-based attacks were discussed earlier in _TCP_ SYN flooding attack methods.
        - __Blind-spoofing__: The attacker cannot see the traffic that is being sent between the host and the target. Blind spoofing is the most difficult type of session hijacking because it is nearly impossible to correctly guess _TCP_ sequence numbers. _TCP_ sequence prediction is a type of blind hijacking because an attacker needs to make an educated guess on the sequence numbers between the host and target. In _TCP_-based applications, sequence numbers inform the receiving machine which order to put the packets in if they are received out of order. Sequence numbers are a 32-bit field in the _TCP_ header. Therefore, they range from 1 to 4,294,967,295. Every byte is sequenced, but only the sequence number of the first byte in the segment is put in the _TCP_ header. To effectively hijack a _TCP_ session, you must accurately predict the sequence numbers that are being used between the target and host.
* _TCP_ Reset "Forged _TCP_ Reset"
    + Maliciously kill _TCP_ communications between two hosts. A _TCP_ connection is terminated by using the FIN bit in the _TCP_ flag, or by using the Reset (RST)
    + In most packets, the RST bit is set to 0 and has no effect. If the RST bit is set to 1, it indicates to the receiving computer that the computer should immediately stop using the _TCP_ connection.