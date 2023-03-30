# MASQUE Working Group - IETF 116

## Time and Date

* Wednesday, March 29, 2023, 09:30 - 11:30 (GMT+9), Wednesday Session I ([See this time in your local timezone](https://www.timeanddate.com/worldclock/fixedtime.html?msg=MASQUE+at+IETF+116&iso=20230329T0930&p1=667&ah=2))
* Location: [4F G403](https://datatracker.ietf.org/meeting/116/floor-plan?room=g403)
* [IETF Agenda Link](https://datatracker.ietf.org/meeting/116/agenda/?show=masque)

## Links

* [Onsite Tool](https://meetings.conf.meetecho.com/onsite116/?group=masque&short=masque&item=1)
* [Meetecho Room](https://meetings.conf.meetecho.com/ietf116/?group=masque&short=masque&item=1)
* [Meeting chat](https://zulip.ietf.org/#narrow/stream/masque)
* [Notes](https://notes.ietf.org/notes-ietf-116-masque) 
* [Minutes](https://datatracker.ietf.org/doc/minutes-116-masque/)

## Administrivia (5 minutes)

* Blue sheets / scribe selection / [NOTE WELL](https://www.ietf.org/about/note-well.html) 
* Agenda revision

## Individual Drafts (70 minutes)

### [QUIC-Aware Proxying Using HTTP](https://datatracker.ietf.org/doc/draft-pauly-masque-quic-proxy/) - _Tommy Pauly_, 15 minutes

Has been discussed many times, recently discussed as part of rechartering discussions.

Tommy presents an overview / recap of the solution: A proxy can reuse target-facing ports for multiple connections, but more importantly clients and proxies can use this to skip encapsulation and encryption for proxied short header packets.

Describes the various capsules used to exchange CIDs and stateless reset tokens.

Presenting recent updates. Recommendations on keep alive, ECN behavior etc. 

Big open issue - should packets be encrypted in forwarded mode?
Two questions to get feedback on:
* How to encrypt?
* Is re-encryption required?

Describes encryption proposals. 
AES-CTR, HCTR2.

Should encryption be negotiable?

**David Schinazi**: Two points: on the first byte, very solvable issue. Header protection use a mask. On figuring the encryption out, don't think we should negotiate it, opens a can of worms. 

**Tommy**: We could negotiate IF we do it though.

**David**: Agree. Also agrees to setting up a design team for the crypto.

**Mike Bishop**: One of the benefits of this is the ability of shoving a packet into a NIC. Worried that the complexity of this might make this difficult. 

**Tommy**: Agree. Want this to be minimal, but secure as possible.

**Martin Thomson**: In favor of adopting. Need to go over how you're doing virtual connection IDs.
AES-CTR is very close to what QUIC packet protection does, the reason for proposing this. 
I don't have a particular opinon on negotiation of this. My starting position is that this should just be turned on.

**Martin Duke**: No hats, two points. Underselling the benefits of encryption. Forwarded mode removes the ability of the proxy to authenticate packets. We need to understand the implications of sending unencrypted.

Congestion control issues, forwarded connections will have advantages over tunneled connections due to potentially large differences in RTTs of these connections. 

**Tommy**: Definitely worth noting those trade offs and how tunnel 

**Martin**: Do you know where the bottlenecks were?

**Tommy**: This is a good thing to discuss, we should do it offline.

**Mirja Kühlewind**: Support adopting this document. Do we actually have a use case for this? Regarding adoption, if we don't have anyone implementing it, we shouldn't include it in this document.

**Tommy**: I want to implement it.

**Mirja**: About congestion control this is definitely something we need to documment. 

### [Proxying Listener UDP in HTTP](https://datatracker.ietf.org/doc/draft-schinazi-connect-udp-listen/) - _Abhijit Singh_, 15 minutes

Presenting current status of Connect UDP - single target facing port per request. Example use-case where it doesn't work, Web-RTC with STUN server relay.

**Eric Rescorla**: Confused, one quic connection and two connect-udp:s right?

**Abhijit**: Yes.

**David Schinazi**: You could have one connection to an intermediary, but request can be distributed to different HTTP servers. 

**Eric**: We had all of these problems with TURN, sounds like you should fix your HTTP semantics instead..

Abhijit contiunes presentation.
Connect-udp with listener, supports infinitely many ports. 

Describes open github issues.

[Issue #12](https://github.com/DavidSchinazi/draft-schinazi-connect-udp-listen/issues/12): **Allow proxy to send public IP and port to client.**
Useful for ICE and prevent some TURN like use of the proxy

**Tommy Pauly**: Do you have any proposal for how to send this information back?

**Abhijit**: Not right now

**Tommy**: Proxy header could be used for this, would be very useful here but also something more generic. This could potentially be split out to a different document.

**Eric**: Once you had that you don't need this.

**Tommy**: To solve this what we really need is the ability to have the same local IP and port.

**Eric**: The way turn works, please assign me an external IP addres, an address independent NAT?

**Tommy**: We need to be able to accept incoming traffic on that

**Eric**: Continues to explain how ICE and TURN works... Probably need to write this down.

**Tommy**: With current connect-udp there is no way to learn what outfacing port and address is or to request that. 

**Eric**: doesn't allow arbitrary connections through the same address.

**David**: The problem statement is that we want to use WebRTC over this. You need some way to tell that this packet comes from a different address.

**Tommy**: There is a difference here, the current draft says give me an IP and port from which I can receive arbitrary... 


[Issue #13](https://github.com/DavidSchinazi/draft-schinazi-connect-udp-listen/issues/13): **Allow restricting accessible IPs**

[Issue #14](https://github.com/DavidSchinazi/draft-schinazi-connect-udp-listen/issues/14): **Compress away IP and port from each HTTP Datagram**

**Ben Schwartz**: Important, should be adopted, good enough starting point. Turn would be a mistake here, mixing up mechanism and policy. Servers are welcome to put a stateful firewall in front of this if you want, should not be part of protocol. 

QUIC optimization to reduce overhead could also be used for SRTP.. 

**Mike Bishop**: You might run a webserver over this. Are we trying to rule that out. Is this in scope?

**Eric Rescorla**: Turn was designed as it was, it was built/designed precisely for this purpose, do not need to open holes in firewalls. This is a much bigger issue than this working group. Is this design what we want, is TURN no longer relevant?
Are we trying to keep the same semantics as before, that's the question we should ask.

### [A Configurable Retransmission Extension for HTTP/3 Datagrams ](https://datatracker.ietf.org/doc/draft-yang-masque-dgram-retrans/) - _Yanmei Liu_ / _Furong Yang_, 15 minutes

Use case: accelerate QUIC in mobile application via UDP tunneling. 

Two ways of tunneling QUIC connections in Masque. Reliable, or unreliable. 

Reliable allows local loss recovery, but introduces HoL blocking. Unreliable oth does not provide local loss recovery.

Which layer to retransmit datagrams. QUIC layer via TP or configure client and proxy to retransmit at HTTP3 layer.

Describes HTTP/3 extension format.

Retransmission limit to control retrans overhead. Client could also send some performance metrics.

Idea is to only retransmit if it would be done before the end-to-end retransmission.

Presents experimental results. 


**Eric Rescorla**: Asks for clarification on the the data presented.

Impact on loss based congestion control. Local loss recovery will hide congestion signals to end-server, can be solved with AQM in proxy. 

Cascaded proxies issue, where should loss recovery be done. 

**Marten Seemann**: Dividing the RTTs does not work because of ack-delays. 

Bottleneck between client and proxy, loss there is the only place where you signal congestion to server. 

Random packet loss should be careful with that.

**Alex Chernyakhovsky**: Wondering if we even need extension to achieve the same results that you've presented here. From my perspective, retransmit limit is actually something that the proxy itself is a much beter position to decide. Don't really see any value in the setting that you're proposing. Can get same effect purely just by implementing the capability on the proxy.

**David Schinazi**: Two points: regarding experimental data. Assume uniform independent random loss? Yes. In my experience this is not correlateble with reality. In my experience loss is highly correlatable.
Point 2. There has been an IPR disclosure on this. That might be hampering adoption. Not cool, could be creating issues with innovation here. 

**Yunfei Ma**: Did some experiments on this in the wild. Want opinions from the WG on how do you differentiate between the congested loss and random loss?

**Chuan Ma**: I really like this proposal, the use case is clear. But in my opinion, this is over complicated by the several layers of QUIC, masque etc. 

**Ted Hardie**: Thank you for bringing this to IETF, good that you provide experimental data. But might not be correlated with reality. This was a problem for TCP way back ago, RLP was introduced to remove all loss from the end-to-end. Required really large buffers, introduced large delays and jitter. Mirja propsed that Hybrid ARQ could be turned off with this solution. Must lie down and think about this. 

**Ian Swett**: Potentially a lot of work to be done here. But more experimentation needed. You can potentially build infinitely large buffers due to this etc. 

**Eric Rescorla**: It would be useful to to measure how much time you save for this.


### [A Sequence Number Extension for HTTP Datagrams](https://datatracker.ietf.org/doc/draft-ihlar-masque-datagram-numbers) - _Marcus Ihlar_, 15 minutes

Adding numbers to HTTP datagrams. Can indicate support by setting a header. Can register use of these number by sending a capsule and you can define context id for that.

Motivation:
ATSS has added quick steering functionality to this. Recently MPQUIC was added as a use for this.

Datagram mode 2: default mode. No packet reordering in HTTP layer.
Datagram mode 1: optional mode. Extension of mode defined RFC9298. 

One use case is reordering. 
ATSS has a redundant steering mode that allows deduplicate data over multiple access. 

Some use cases for where this is used:
Campus where existing Wi-Fi coverage is augmented with 5G radio. 
Emergency response teams, simultaneously connected over multiple accesses.

**Alex C**: This extension looks fine from wireshark prespective. Wonder based on appplications that you're describing if it goes far enough. Wire format part is not the interesting part. It's how you layer things to make this work well.

**Marcus**: I agree that this is a deep problem.

**Martin Seeman**: QUIC was designed with the point that we have the packet as early as possible. Don't understand why we'd have something in the middle of the network artificlally delaying my packet.

**Marcus**: That's assuming if we have QUIC. This is also for non-QUIC applications. We might be using QUIC or other protocols. For QUIC and other modern transport, reordering is already done well, but for others, you might need this extension. Having sequence numbering is simple way to achieve that. I'm sure there's other ways as well.

**Ben Schwartz**: Should be open to extensions. Confused about how this should work. In duplication layer, are you saying that QUIC packets containg H3 data would be duplicated between HTTP cient and MASQUE proxy?

**Marcus**: Assuption that you're running multipath QUIC.

**Ben**: Even if that's the case ___

**David Schinazi**: Chairs pointed out that earlier IPR comment was out of line so apologizing.
Regarding draft, agree with discussion so far. Sooner you get packets the better. Should add text to clearly scope for cases that dont need this.

**Tommy Pauly**: Thank you for this. Good discussion. Probably necessary for practicality, but shouldn't be flagship reason for this document. I think that the deduplication is quite interesting. If we have MPQUIC, whe you're doing streams, similar to where at the transport layer, hadnginling uniqueness of a stream frame. So when I send particilar chunk of data for stream zero on one path and another path that can be duplicated by the QUIC layer, and that's great. You need duplication beacause that is something WUIC datagrams especially punt up to upper layers. Would like to see that as flagship use case of document since it;s a really good feature.

**Ian Swett**: Number of use cases is hard to reason about, but certainly the deduplication case.

**Yunfei**: We use this at Alibaba as well, so important work. But not exactly same format, so can share some of our data later on.

**Marcus**: That would be great.

(Marcus continues presenting last slide)


### [HTTP Datagrams, UDP Proxying, and Extensible Prioritization](https://datatracker.ietf.org/doc/draft-pardue-masque-dgram-priority/) - _Lucas Pardue_, 10 minutes

Priorities matter when there is a a bottleneck. 

A masque proxy uses a single connection and multiplexes many connections to backend servers. Most likely that the bottleneck is on the client to proxy side.

Classic CONNECT method describes this problem in RFC??

Clear problem statement: Few tools to help signal that some streams/flows are more important than others.

Integrate datagram flows into the extensible priority model. Add simple parameter and some guidance. 

**Alex Chernyakhovsky**: I feel that we have both connect-udp and connect-ip on both ends of the spectrum. We're solving the wrong problem here. Ýou could just use connect-ip with DSCP bits set. 

**Lucas**: We should provide guidance on how to use these things. 

**Ian Swett**: We should be working on this, and this is useful.

**Martin Thomson**: I tend to think that, we have this problem when we talk about prioritaztion. We conflate applying with signalling priorities. There are mechanisms that we use that are very coarse, it will only allow you to distinguish between sessions. Does not help use distinguish between more fine grained data. Doing something here is good.

**Ben Schwartz**:
DSCP solves this for connect-udp. You're concerned about downstream direction. DSCP set by target tells you how to prioritize.

The client knows the bandwidth on the client / proxy link, it can choose how much bandwidth it allocates on that link.

**Alan Frindell**: Priority enthusiast. Interesting work, similar interest in Webtrans. In WT there's a mapping over h2, and similar over capsules, they would innerit priorites from the connect stream. 
We're also thinking about this in MoQ. 

**Lucas**: Does not just have to be a header, several ways how this can be communicated, such as capsules. 

**Kazuho Oku**: Is it a reason to have an urgency associated with datagrams when it is optional to use datagrams.

**Lucas**: You get flexibility, you can for instance prioritize capsules over datagrams if important messages are sent there. 

### Discussion of relay capability configuration, advertisement, and discovery [similar to IKEv2](https://www.rfc-editor.org/rfc/rfc8598.html), _Tommy Pauly and Ben Schwartz_, 20 minutes

No document here, just a discussion on whether this is useful, in scope or out-of scope. 

Tommy shows various ways proxies can be configured. 

What are the things a client might need to know about a proxy? Services, authentication etc.. 

Tommy presents split DNS approaches.

Open Questions: 
* Should configuration elements be standardized?
* Where should work be done?
* Can we dra a line between missing functionality etc and out-of-scop policy items?

**David Schinazi**: In a lot of VPN cases you need certificates on both sides, users will never type that in by hand. You are going to need a file, in those cases we don't need anything. We have a format we can add keys to that format.

**Tommy**: I'm thinking something more dynamic, configurations can change dynamically.

**Ben Schwartz**: state of the art is proprietary. Those should not be proprietary and standardized.

**David Schinazi**: I agree, but one more case where this is not needed is Apple private relay, things are pre-agreed.
There are cases where this is needed though, when you need to enter a username, password etc. So when this is needed is when things can be typed in and when things are dynamic.

**Alex**: I'm agreeing with David which I didn't really expect. Your description of VPNs are not aligned with my mental model. I think of them as please suck up all my traffic. My understanding is that we don't work on here's a set of domains that are accessible..

**Tommy**: I'm mostly thinking of Connect-udp here where we usually do not DNS through the proxy. It's very inefficeient to try to attempt to connect to multiple masque proxies to figure out if they can serve the request.

**Eric Orth**: This seems like a much more general problem than for this specific working group. We need to solve the more general proxying problem, that's probably not here.

**Eric Rescorla**: There are two categories of information here. Trust the proxy to do something or do not proxy to do something. 

**Tommy**: this is the same thing the IKEv2 needed to solve.

**Martin Duke**: The current charter puts this explicitly out of scope. 
Will take the AP to take this to the IESG. There is not an overwhelming interest in standardizing this, but there are some interested people, I will take ownership of this.

**Mike Bishop**: Third time we talk about this. These are good thing to plug into a framework, but we don't know the framework.

