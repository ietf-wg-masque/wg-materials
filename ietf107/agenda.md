# MASQUE BoF material for IETF 107 

## Agenda

- Introduction and rules of engagement (5 minutes)
- Problem statement and MASQUE framework (20 minutes)
- Use-Cases (15 minutes)
- Requirements (10 minutes)
- Charter text discussion (45 minutes)
- BoF questions (30 minutes)
  
## Proposed Charter

Many network topologies lead to situations where transport protocol proxying is beneficial. For example, proxying enables endpoints to communicate when end-to-end connectivity is not possible and can apply additional encryption where desirable (such as a VPN). Proxying technologies such as SOCKS and HTTP(s) Connect exist, albeit with their own shortcomings. For example, SOCKS signalling is not encrypted and HTTP Connect is limited to TCP.  

In contrast, QUIC is a viable candidate protocol for proxying arbitrary traffic, as it provides secure connectivity, multiplexed streams, and connection migration. Further, HTTP/3 supports an established request/response semantic that can be 
used to set up and configure services.

Using QUIC as a proxying technology allows for proxying of both reliable stream (TCP) and unreliable datagram (UDP) flows. For stream flows, QUIC streams provide reliable in-order delivery across the client-proxy link. QUIC datagrams provide for unreliable data transmission, which allows for transporting UDP and other unreliable flows via a proxy without introducing potentially redundant or unnecessary recovery mechanisms. In addition, QUIC can carry both types of flows over the same connection while taking advantage of a unified congestion controller.

The primary goal of this working group is to develop MASQUE (Multiplexed Application Substrate over QUIC Encryption), a framework that allows configuring and concurrently running multiple proxied stream- and datagram-based flows inside a QUIC connection. The MASQUE framework will specify a signaling protocol between client endpoints and MASQUE servers for negotiating proxy services and corresponding parameters. The group will also specify support for an initial set of client-initiated services such as HTTP, QUIC, and IP proxying that MASQUE servers support. The group may subsequently recharter to consider additional services or applications.

Proxy services that extend the signaling of the base MASQUE protocol can be adopted by the group by creating a new milestone with AD review. Specifying MASQUE server discovery mechanisms is out of scope for the group.

If MASQUE requires any extensions to existing protocols, the group will coordinate closely with the respective group responsible for maintaining that protocol, such as the HTTPBIS, QUIC, or TLS working groups.
    
## References:
- https://datatracker.ietf.org/doc/draft-schinazi-masque/
- https://datatracker.ietf.org/doc/draft-schinazi-masque-obfuscation/
- https://datatracker.ietf.org/doc/draft-kuehlewind-quic-substrate/
- https://datatracker.ietf.org/doc/draft-piraux-quic-tunnel/
- https://datatracker.ietf.org/doc/draft-kuehlewind-quic-proxy-discovery/
