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
multiple proxied stream- and datagram-based flows inside an HTTP connection. These mechanism(s) are collectively called
MASQUE. The group will specify HTTP and/or HTTP/3 extensions to enable this functionality.

The group will focus on extensions for a limited set of client-initiated services: (1) UDP CONNECT and (2) IP proxying.
Server-initiated services are out of scope.

Exercising the extension points defined by CONNECT-UDP and CONNECT-IP helps to make it easier to support new use cases
or accommodate changes in the environment in which these protocols are deployed. The initial set of extensions will be
in support of UDP listening and QUIC-aware proxying. Additional extensions that provide missing functionality, improve
performance, or otherwise ease deployability for use cases may be adopted where there are multiple implementation
and/or deployment proponents. 

Extensions to HTTP Datagrams will be coordinated with HTTPBIS. Extensions that solely relate to generic proxying
functionality, and are not specific to the core MASQUE documents, are out of scope. 

Specifying proxy server discovery mechanisms is out of scope. New congestion control and loss recovery algorithms are
also out of scope. However, the working group will consider implications of tunneling protocols with congestion control
and loss recovery over MASQUE proxies, and may issue recommendations accordingly. 

The working group will consider how the protocols it defines might operate over versions of HTTP that use TCP rather
than QUIC, for use when QUIC is unavailable. This might include defining alternative extensions specifically for use in
these HTTP versions.

Multicast is out of scope. Extensions providing generic capabilities that might include multicast are acceptable on the
basis of their application to other use cases. For example, a generic IP proxy might be used for both unicast and
multicast communication. For such generic capabilities, designs will not explicitly preclude multicast, but they will
not focus on multicast-specific features.

Impacts on address migration, NAT rebinding, and future multipath mechanisms of QUIC are not anticipated. However, the
working group should document these impacts, or those of any other QUIC developments, if they arise.

The group will coordinate closely with other working groups responsible for maintaining relevant protocol extensions,
such as HTTPBIS, QUIC, or TLS. It will also coordinate closely with ICCRG and TSVWG on congestion control and loss
recovery considerations, and intarea for IP Proxying.