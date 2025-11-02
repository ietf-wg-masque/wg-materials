# MASQUE Charter

Many network topologies lead to situations where transport protocol proxying is beneficial—for example, enabling endpoints to communicate when end-to-end connectivity is not possible, or applying additional encryption such as in VPNs. Proxying can also improve client privacy by hiding client IP addresses from target servers. Existing proxying technologies (e.g., SOCKS and HTTP CONNECT) have limitations: SOCKS lacks encryption, and HTTP CONNECT is limited to TCP.

The MASQUE working group has developed mechanisms that allow configuring and concurrently running multiple proxied stream- and datagram-based flows inside an HTTP connection. These include CONNECT-UDP and CONNECT-IP, collectively known as MASQUE. MASQUE leverages HTTP semantics, multiplexes flows over streams, uses a unified congestion controller, encrypts flow metadata, and enables unreliable delivery suitable for UDP and IP-based applications.

The MASQUE working group is now in the process of completing its remaining deliverables:

 1. A mechanism for proxying Ethernet over HTTP
 2. An extension to CONNECT-UDP that is specific to proxying QUIC
 3. An extension to CONNECT-UDP that allows proxying bound sockets
 4. An extension to CONNECT-IP that allows conveying DNS and PREF64 configuration
 5. An informational architecture document about MASQUE
 6. An extension to CONNECT-UDP for ECN and DSCP support
 7. An extensions to CONNECT-UDP and / or CONNECT-IP to enable compression
 8. A definition of qlog nomenclature for existing MASQUE mechanisms

These documents are being finished in line with MASQUE’s previous charter: 

* The intended status is Standards Track unless otherwise noted, but the WG may downgrade if it believes that is appropriate for the ultimate document maturity level.
* Services that a proxy initiates without any prompt from a client are out of scope.
* Extensions that solely relate to generic proxying functionality, and are not specific to the core MASQUE documents, are out of scope.
* Specifying proxy server discovery mechanisms is out of scope. New congestion control and loss recovery algorithms are also out of scope. However, the working group will consider implications of tunneling protocols with congestion control and loss recovery over MASQUE proxies, and may issue recommendations accordingly.
* IP multicast is out of scope. Designs need not explicitly preclude multicast, but they will not focus on multicast-specific features.
* Impacts on address migration, NAT rebinding, and future multipath mechanisms of QUIC are not anticipated. However, the working group should document these impacts, or those of any other QUIC developments, if they arise.

Beyond finalizing these documents, the MASQUE working group is not accepting new work items. Any proposals for new extensions or functionality should be directed to appropriate alternative working groups such as HTTPBIS, QUIC, or INTAREA, depending on the nature of the work.

MASQUE will continue to coordinate closely with HTTPBIS, QUIC, TLS, INTAREA, ICCRG, CCWG, and IEEE 802.3 as appropriate.

The MASQUE working group is expected to conclude once its current documents are published.
