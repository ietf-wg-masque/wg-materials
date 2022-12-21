# MASQUE Charter

Many network topologies lead to situations where transport protocol proxying is beneficial. For example, proxying
enables endpoints to communicate when end-to-end connectivity is not possible, or to apply additional encryption where
desirable (such as a VPN). Proxying can also improve client privacy, e.g., by hiding a client's IP address from a target
server.

Proxying technologies such as SOCKS and HTTP(S) CONNECT exist, albeit with their own shortcomings. For example, SOCKS
signalling is not encrypted and HTTP CONNECT is currently limited to TCP. In contrast, HTTP/3 is a viable candidate
protocol for proxying arbitrary traffic, as in a single connection it provides secure connectivity, multiplexed streams,
address migration, and a unified congestion controller. An HTTP/3 datagram construct built on top of QUIC datagram
frames would provide for unreliable data transmission and enable transporting UDP and other unreliable flows via a
proxy. Moreover, it would not introduce potentially redundant or unnecessary recovery mechanisms. Lastly, HTTP supports
an established request/response semantic that can set up and configure flows for different services.

The primary goal of this working group is to develop mechanism(s) that allow configuring and concurrently running
multiple proxied stream- and datagram-based flows inside an HTTPS connection. These mechanism(s) are collectively called
MASQUE. The group will specify HTTP and/or HTTP/3 extensions to enable this functionality.

The group will focus on a limited set of client-initiated services: (1) UDP CONNECT and (2) IP proxying.
Server-initiated services are out of scope. The working group will first deliver a protocol solution for UDP CONNECT and
a requirements document for IP proxying. Once both are complete, the working group will focus on a protocol solution for
IP proxying.

Exercising the extension points defined by CONNECT-UDP and CONNECT-IP is important to ensure their continued useability.
The working group will consider extensions to these documents that provide missing functionality or enhance performance
for real world use cases. The initial set of extensions will be in support of UDP listening, QUIC aware proxying, and
datagram priorities. Extensions that help enable CONNECT-UDP or CONNECT-IP services to coordinate or work well in the
ecosystem of other services may be considered. Extensions to HTTP/3 Datagrams will be coordinated with HTTPBIS.
Extensions that solely relate to generic proxying functionality, and are not specific to the core MASQUE documents, are
out of scope.

The working group will consider fallback to versions of HTTPS that operate over TCP as a mitigation to UDP or HTTP/3
blocking. Specifying proxy server discovery mechanisms is also out of scope. Moreover, the working group will consider
implications of tunneling protocols with congestion control and loss recovery over MASQUE proxies, and may issue
recommendations accordingly. New congestion control and loss recovery algorithms are out of scope. Multicast support is
out of scope. However, the group may specify extension points that would enable future work on multicast.

Impacts on address migration, NAT rebinding, and future multipath mechanisms of QUIC are not anticipated. However, the
working group should document these impacts, or those of any other QUIC developments, if they arise.

The group will coordinate closely with other working groups responsible for maintaining relevant protocol extensions,
such as HTTPBIS, QUIC, or TLS. It will also coordinate closely with ICCRG and TSVWG on congestion control and loss
recovery considerations, and intarea for IP Proxying.