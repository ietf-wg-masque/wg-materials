# MASQUE IETF 117

## MASQUE IETF 117
Monday, July 24, 2023, 09:30 - 11:30, Monday Session I

- MASQUE Logo (badge) was designed.
- There are stickers... We like stickers.

## Targeted Extension Individual Drafts 

### QUIC-Aware Proxying Using HTTP - Tommy Pauly

Preliminary decision on mailing list to scope threat model to passive attackers

Christian Huitema (remote): I don't feel comfortable with adding the special treatment. I wish it could be negotiated separately. It would be good to limit the number of open UDP ports.

Tommy: Both client and proxy need to negotaiate this. Not having to re-encaps for muliple proxies is more efficient when you have this.

Eric Rescorla: I share some of Christian's discomfort. This needs real work. Disagrees with the preliminary decision to scope threat model.

Tommy: I think we should dig into this more. If we forward without encypting, then this is like a NAT that you have a handshake with. The secuirty properties resemble that of not hoing via the proxy.

Eric: I want to be able to quantify the difference in security assumption between double encryption and this proposal. There are things you could do that would be better than this, but worse than just doing the full encapsulation.

Watson Ladd: I think there is history with this tagging approach. A chain of proxies do not necessarily get the final information. There is a whole history here with multiple proxies.

Christian: We have to be careful that the proxy does not make optimisations prematuterly, that could be done later. otherwise, optimisations can have consequences.

Mirja: I disagree with Eric ... there are different use cases that might benefit from this.

David Schinazi: I agree with Mirja - though for the Tor use case we do have different needs and we need to seperate the two use cases (e.g. Apple Private Relay does not need the same as Tor).

Eric: The baseline should be full encapsulation.

Tommy: ... that makes sense.

Martin Duke (on behalf of Brian Trammell) - by removing the double encaps, it seems you can inject data in the middle on the proxy path that the proxy might admit.

David on the crypto parts: Design from Ben Schwartz. Plaintext is encrypted packet here. Property different. Do simpler things than QUIC header protection. Want to form a design team.

QUIC Version compatibility (Issue #83)

Martin: What assumptions are we making about future versions of QUIC beyond the QUIC Invarients? I think this seems straightforward to write. This can be an optional field for the stateless reset.

Mike Bishop: Version based capabilities how to plug in capabilities for the future?

Congestion Control Loop (#81)
CID Compression (#74) - should this be in the draft?
Eric: Is this also an issue for the next talk? Overhead can be large for small packets. This could be a separate extension/topic.

Martin: I don't agree.

Mirja: Why the same draft? - can't we define separetly?

Tommy: We need some serious design of the encryption - do we need a WG design team? 

David: Should we adopt this?

Chairs: Is there a clear target (or targets) use case for this document?
For: 29. No: 6.

Chairs: Is there interest in adopting this as a starting point for work?
For:  39  No.: 1

We will not publish this without security analysis.

Eric Rescorla: Formal adoption of the spec should require a consensus call on the actual spec and crypto design.

### Proxying Listener UDP in HTTP (draft-schinazi-connect-udp-listen)- Abhijit Singh

Eric: It seems like we are reinventing each feature of TURN. We should just steal each bit of TURN and look at what mechanisms we need.

Lorenzo: We want applications to not have to deal with complexity (esp in Android that has low level primitives). Ideally we make this like a UDP socket. Think about how to emulate end-to-end connections.

David: This is reinventing turn; we previsouly reinvented socks... ~~"We're running the Internet over HTTP now but don't tell anyone"~~ This is essentially an *un*connected UDP socket.

Lorenzo: +1 David. The port does not change when you use the UDP port, we need to emulate the sockets API. 

Eric: This seems fine to do, we should copy/paste TURN into the spec. A standard firewall allows only outgoing connections. TURN was specifically designed not to by-pass the firewall rules for incoming packets. This should be a conscious decision.

David: TURN was distinguishable at the time. MASQUE is designed to look like normal traffic, that is a difference with TURN. We should have a conversation about how MASQUE will implement this.

...: SSH also is used. I don't think we should worry about restricting acessible IPs.

Eric: I don't this precludes adoption. We just need to decide.

Lorenzo: Automatic timeouts can be painful for NATs. It would be nice to get away from this. The security system can police TURN, but here the data is opaque, this is different.

...: What if we have multiple proxy instances? Do we need to look ar level 4?

David: In our implementation we have ingress and egrees IP addresses, and the outbound traffic uses a separate address range (through a different load balancer)... this problem needs to be solved for connect UDP also.

Christian: We need to consider the impact on deployment: ICE and server from home scenarios. We should think about these separately. ICE knows the peer IP address. Dynamically unrestrict for ICE.

Lorenzo: Do not do anything too restrictive.


David: If you send 2 separate connect-udp, this might end up with 2 different IPs.

Eric: You should compress the IP info (like in TURN), but you still have a QUIC header - overall it's not an amazing saving.

Lorenzo: If you want a constant IP address. This means protocol changes to talk to the server, to communicate app identity - which has privacy 
implications. We should think about whether the app can get a stable IP address.

Teng: There is a 3GPP proposal to use MPQUIC to ... this is useful.

Alex: There is subtantial overlap with connect-ip; this overlaps ad connect-ip would let you do this as a special case framing (e.g. to get a stable previously used ip with some server policy). Don't think of this as conenct-udp but think of it as a part of the MASQUE features.

Chairs: Is there interest in the room in adopting this as a work item?
For: 38 No: 2

- Chairs will confirm on the list.

## Other Individual Drafts 
### Communicating Proxy Configurations in Provisioning Domains - Tommy Pauly

Mike Bishop: This is similar to the draft from Ben Schwartz. Maybe those proposals could be merged. This one seems to be useful.

Tommy: PvDs do have a well-known allocated, that could be good enough.

Lucas Purdue: This is needed. Some proxies need auth; some do not. The WG might help understand these config details.

Tommy: You could add to the dictionary to do that.

Martin: I want only one of these things to exist. Please only one configuration method! We should articulate the trust relationship of the PvD and 
what it can supply, rather than a client stumbling over one of the config files, we need to think how the client discovers this.

Tommy: Totally agree. We should specify a better trust model

David: +1 and let's do it INTAREA.

Mirja: Do you also need to find the proxy?

Tommy: If I already trust a proxy, it is easy to find what also can be used.

Mirja: Would it fit in MASQUE?

Tommy: We should talk.

- Chairs will talk with INTAREA on best home for this work.
    
### Proxying Ethernet in HTTP - Alejandro Sedeño

Is there interest in this?
Eric: Has someone looked at other L2 transport techniques that have been done?

Markus Ihlar: There is interest in this. 3GGP are discussing ATSS for Ethernet sessions.

Watson Ladd: Why? Just because you can, why do we need to do this?

Lorenzo: Ethernet can't be routed.
- You cna use it to bridge to a remote ethernet segment.

Mirja: ATSS for Ethernet might be thought of as a very generic solution.

Alex: This is different to the other MASQUE connect functions. This could be used to access top-of-rack equipment and that probably means there are 
good use cases.

-: Are you looking at multicast and broadcast?
I expect work to be done here.

Chairs: We did not include this in the last charter update. We are going to see a show of hands on this, if a new charter allows this:
For: 31  No: 18

Chairs: Would anyone like to say why they think this is not a good candidate for adoption, this would be helpful.

Watson Ladd: I just don't see the use case that justifies this.

Lorenzo: How much will this cost the WG?

Nygren: The concept is interesting. Perhaps talk to other areas to check this wrt to VXLAN.

- Chairs will talk after the meeting.

### Datagram Numbers - Marcus Ihlar
    
Martin: The stream mode is an outlier. 20-30% more RTT is a lot. ATSSS seems to have a negative impact on performance.

Lars: This is pretty bad - even for low bandwidth, at higher rates the buffering requirements increase.

David: This is a performance enhancing middlebox. QUIC handles reordering better than delaying to fix reordering. This can make things work.
There is a need to look at this point.

Is there interest in this topic?
For: 5  No. 23  

See the SADCDN proceedings and consider the slide meeting.