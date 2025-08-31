# MASQUE Charter

Many network topologies lead to situations where transport protocol proxying is beneficial. For example, proxying
enables endpoints to communicate when end-to-end connectivity is not possible or to apply additional encryption where
desirable (such as a VPN). Proxying can also improve client privacy, e.g., by hiding a client's IP address from a
target server. Proxying technologies such as SOCKS and HTTP(S) CONNECT exist, albeit with their own shortcomings. For
example, SOCKS signalling is not encrypted and HTTP CONNECT is currently limited to TCP.

The primary goal of this working group is to develop mechanism(s) that allow configuring and concurrently running
multiple proxied stream- and datagram-based flows inside an HTTP connection. The group has specified CONNECT-UDP and
CONNECT-IP, collectively known as MASQUE, to enable this functionality. MASQUE leverages the HTTP request/response
semantics, multiplexes flows over streams, uses a unified congestion controller, encrypts flow metadata, and enables
unreliable delivery suitable for UDP and IP-based applications.

The MASQUE working group will now develop a third core document, CONNECT-ETHERNET, an analogous means of delivering
Ethernet frames over HTTP streams. It will also develop HTTP extensions, which might be specific to the HTTP version,
to the core client-initiated CONNECT-UDP and CONNECT-IP functionality. Services that a proxy initiates without any
prompt from a client are out of scope.

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

IP multicast is out of scope. Extensions providing generic capabilities that might include IP multicast are acceptable
on the basis of their application to other use cases. For example, a generic IP proxy might be used for both unicast
and multicast communication. For such generic capabilities, designs will not explicitly preclude multicast, but they
will not focus on multicast-specific features.

Impacts on address migration, NAT rebinding, and future multipath mechanisms of QUIC are not anticipated. However, the
working group should document these impacts, or those of any other QUIC developments, if they arise.

The group will coordinate closely with other working groups responsible for maintaining relevant protocol extensions,
such as HTTPBIS, QUIC, or TLS. It will also coordinate closely with ICCRG and TSVWG on congestion control and loss
recovery considerations, and intarea for IP Proxying.

When these deliverables are complete, the working group will either recharter to reflect additional work or close.