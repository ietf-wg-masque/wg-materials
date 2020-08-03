# MASQUE - IETF 108

**Meetecho**
https://meetings.conf.meetecho.com/ietf108/?group=masque

Agenda
https://datatracker.ietf.org/doc/agenda-108-masque/

CodiMD
https://codimd.ietf.org/notes-ietf-108-masque

Jabber Room
masque@jabber.ietf.org

Mailing List
masque@ietf.org

Note Well
https://datatracker.ietf.org/submit/note-well/

WG Materials
https://github.com/ietf-wg-masque/wg-materials/tree/master/ietf108


Jabber Logs ([CodiMD](https://codimd.ietf.org/s/ry5SScaxP) | [edit](https://codimd.ietf.org/mULDu7HXRzGNL50_F2UHow)) ([GitHub](https://github.com/ietf-wg-masque/wg-materials/blob/master/ietf108/jabber.md))


## CONNECT-UDP
*David Schinazi*

[draft-schinazi-masque-connect-udp-00](https://tools.ietf.org/html/draft-schinazi-masque-connect-udp-00)

### Slide 10

David Schinazi: Should CONNECT-UDP and H3-Datagram be a single draft or separate drafts?

Mike Bishop: I think there is value in keeping CONNECT-UDP and H3-Datagram separate.  If we had had datagrams earlier we might have used them for other things in H3.

### Slide 11: CONNECTX vs CONNECT-*

Ted Hardie: Friction around these new features will be high, so I don't think we will need the flexibility of CONNECTX.  We're not going to have 20 of these.

Brian Trammell: Same as Ted, different reason.  You might be in a situation where there is a chain of intermediaries, but the difficulty is actually in the implementation of each of these protocols.  I don't want to delay implementation while we try to figure out CONNECTX.

Tommy Pauly: If we're doing them all in MASQUE together there is room to connect them into a unified proposal.  We don't need a separate CONNECT-UDP and CONNECT-QUIC.  It may need more knobs than basic CONNECT-UDP, but we don't need to worry about covering future use cases once we have something that works for our present use cases.

David Schinazi: CONNECT-UDP is a minimal -00 draft, so I think we will need more knobs to cover some foreseen use cases.

Martin Thomson: This design is to take what you learn from the design but not necessarily the spelling.  There are going to be intermediaries who want to know about one of these methods, especially if you're terminating it, where you need to know about the specific protocol.  They all have different functionality so I would prefer to identify the things that are common and make them the same, without having to share a method.  Prefer composition, not inheritance.

David Schinazi: Yes, we can define CONNECT-QUIC with reference to CONNECT-UDP, mentioning the differences.

Victor Vasiliev: It is unclear to me why these things are separate methods.  It seems like most of them do the same thing: they push datagrams back and forth.  They sound like different content-types or payloads.  If we adopt the HTTP route in WebTrans, you could layer this all on WebTrans.

David Schinazi: There's definitely a lot of overlap to WebTransport, and we might end up layering one on top of the other, but I would prefer to keep them separate for now.  We may figure out how to unify these things eventually, either by layering or by sharing components.

Magnus: We need to have a discussion about what MASQUE really is, and what it means to forward datagrams, before we make these decisions.  Let's discuss what functionality and extensions we need before making a decision here.

David Schinazi: Yes, we're not trying to make a decision today.

Erik Nygren: Another reason a CONNECTX might make sense would be for cross-protocol interactions. In-particular, for handling ICMP(v6) responses to UDP messages such as for PMTUD/PTB. Although the proxy may/will need to translate those regardless so it may be that that's a special-case and just having a general mechanism for relaying those back across the various CONNECT-* methods would also be fine.

### Slide 12: Adoption

Eric Rescorla: I think this is good enough for adoption.  I have some concerns about proxy handling: most clients are not even aware of their proxies, hence the push to move awareness of proxies all the way back to the client.  Instead, I think we should be trying to make chains of proxies where each step has awareness of the next step.

David Schinazi: This issue is Slide 8.  Seeking input on this topic.

Brian Trammell: I share a bit of EKR's concern.  There's an architectural moment to consider how we consider proxies.  To change our notion of transparency as related to chains of proxies.  I don't see that as an impediment to adoption of this draft, but I think we should take it as a work item for this group to map out how clients establish chains of proxies.

David Schinazi: I was told that to support HTTP semantics this kind of chaining had to be supported, but if as a WG we decide that is a non-goal we can certainly do that

Martin Thomson: I think you're going to need to use frames anyway.  EKR's probably right: you're going to need every hop in the chain to indicate that it's willing to do this.  Otherwise you'll get dumb forwarders that forward the method, resulting in problems when both endpoints support the method but the middle does not.

David Schinazi: This is making me lean more toward making this explicitly a non-goal, requiring a specific setting on the single-hop connection.

EKR: Part of the confusion is that our use of the word "proxy" is mixing several concepts.  There are reverse proxies (between client and backend) and forward proxies (client does selection), and among those there are nested (like Tor) and unnested.  It's not clear why you care about the topology of the server farm, but it's obvious why you care about the topology of your Tor tunnels.  We need some terminology to avoid being confused.

David Schinazi: Yes, we can do a lot better when avoiding confusion.  I think we should focus on forward proxies.

Chairs: Hum result favors adoption, but we will confirm on the list.

Martin Thomson: I see some people hummed against.  I would like to hear why, here or on the list.
David Schinazi: Yes, me too.

## IP Proxying Requirements
*Dallas McCall*

[draft-cms-masque-ip-proxy-reqs-00](https://tools.ietf.org/html/draft-cms-masque-ip-proxy-reqs-00)

### Presentation

Dallas McCall: On authentication: If the underlying TLS connection doesn't use client authentication, MASQUE should still be able to require client identity verification.

Eric Rescorla: What do you mean by "cryptographically verifiable"?  An OAuth token doesn't necessarily qualify.

Dallas McCall: We just mean something that the server can trust.

Alex Chernyakhovsky: When we say "cryptographically verifiable", we don't mean that the data has some intrinsic property, but that it can be verified by the server.  We want to be able to support non-WebPKI authentication, because we don't want to lock people into client certificates

Eric: I don't understand what "cryptographic" is doing here.

Dallas: I think we need to clarify the meaning here.

Dallas (On route negotiation): We may be able to use a pre-existing protocol for route negotiation.

### Discussion

Ben Schwartz: Why do we need to reinvent IP tunneling, when there are already so many standards for IP tunneling?

Dallas McCall: Not presupposing that we're going to reinvent this, just laying out the requirements that we will need to satisfy.

David Schinazi: I'm seeing lots of good input in Jabber: please also send those comments to the list, or to the authors by email or github, so we can incorporate those suggestions.

## QUIC Proxying
*Tommy Pauly*

[Slides](https://www.ietf.org/proceedings/108/slides/slides-108-masque-optimizing-proxies-for-quic-00)

Tommy Pauly: No draft yet on this, but we're working on it

Tommy Pauly: This is particularly interesting for multi-hop forward proxying like Tor.

Tommy Pauly: "CONNECT-QUIC" is a placeholder here, separate from the question of whether to use one method or many.

Tommy Pauly: The new mode here is "forwarding" instead of "tunneling".

Martin Thomson: From what I've seen on the slides, I have serious reservations as to whether this is going to achieve the goals you've set.  What are those goals?

Tommy Pauly: The goal is to allow multiple proxy hops for QUIC without encapsulation and encryption overhead for each hops.  We don't have to keep adding DATAGRAM frames and reducing MTU and adding encryptions.

Martin Thomson: That's a reasonable goal, but I don't think this is going to work for a proxy that has to manage multiple connections.  Did you say that the client sets the connection IDs that the proxy expects to use?

Tommy Pauly: Whenever the client becomes aware of a connection ID, it informs the proxy and requires a confirmation before it will start using that connection ID.

Eric Rescorla: I understand the performance objective, but I don't think I understand the security objective.  You said "tor-like", but Tor has some extremely specific properties and I don't understand how this prevent private collusion.

Tommy Pauly: It won't have the same security properties as a Tor-like thing, especially because you may see the same content going through on both sides of the proxy.  You won't  see more than if you were on path already.  The goal is to get IP address obfuscation but we're not necessarily concerned about reconstructing the path if you can see all the links.

Eric Rescorla: I think this needs a clearer threat model.

Tommy Pauly: We're just trying to point out that this mode exists, and could be used when it's appropriate.

Eric Rescorla: I'm trying to figure out whether this has interesting security properties.

Tommy Pauly: Connection ID mapping is especially difficult when multiplexing connections onto a single client-proxy 5-tuple.  Beyond collusion protection, we also need to make sure that you're not vulnerable to forwarding replayed packets.

Ben Schwartz: This is not so similar to CONNECT-UDP or old-fashioned CONNECT, because it breaks out of the TLS stream.  Recommend focusing on a non-HTTP proxy substrate like SOCKS-6.


## Transport Considerations for IP and UDP Proxying
*Magnus Westerlund*

[draft-westerlund-masque-transport-issues-00](https://tools.ietf.org/html/draft-westerlund-masque-transport-issues-00)

### Presentation

Magnus Westerlund: (There's also a slide on Hop Limit that we probably won't have time to discuss)

### Discussion

Alex Chernyakhovsky: Normal UDP applications were not previously aware of any of these extensions.  Does not now have to implement them in user-space?

Magnus Westerlund: No, the MASQUE interface needs to expose that to the upper layer protocol.  If you want to reproduce the functionality of the socket API, you'll need to expose that somehow.

David Schinazi: You've used terms like "need" and "requirements".  That seems to be requirements for extensions to CONNECT-UDP.  But these aren't universally necessary and can't always be implemented.

Magnus Westerlund: There are certainly things that are difficult to implement, but we certainly need the protocol to have extensibility to support these uses.  I think ECN and fragmentation are going to be particularly important.  So there is a base set that is needed for a datagram service that is similar to end-to-end IP or UDP, and then there are some extensions.

David Schinazi: I think ECN and ICMP don't always get through the internet today, so I think we can move forward without them.

Magnus Westerlund: The DF bit is an example of something we really need, because the client can't detect PMTUD failures otherwise.

Ian Swett: CONNECT-QUIC does mitigate a lot of these issues.

