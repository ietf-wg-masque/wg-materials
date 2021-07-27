# MASQUE Working Group Agenda - IETF 111

Monday, July 26 2021, 16:00 - 18:00 (UTC), Monday Session III

## Administrivia (5 minutes)

No agenda bash.
Meetecho intro.
School jabber scribe that it's included in Meetecho.
Note well.

Scribe: Watson Ladd
Jabber Scribe: Sean Turner

## CONNECT-UDP and HTTP/3 Datagrams (60 minutes)

Drafts:
- [CONNECT-UDP](https://datatracker.ietf.org/doc/draft-ietf-masque-connect-udp/)
- [HTTP/3 Datagrams](https://datatracker.ietf.org/doc/draft-ietf-masque-h3-datagram/)

Notes:
* Introp, updates, issues
* quic-dev slack. Send Chris or Eric to get added to the server if you want to play interop games (quic-chairs@ietf.org)
* Previously, CONNECT-UDP, all version of HTTP, across intermediaries. HARD
* But some people wanted to reuse part of it for HTTP/3 Datagrams
* Why now? QUIC lets you drop misses
* So split draft into two: HTTP/3 Datagrams, Connect-UDP on top
* Interim redesigned everything, merged some PRs, uploaded revisions
* What changed?
* Interop: previously full matrix. Now just Ericcson and Google working both ways due to time to implement
* Apple shipped iCloud private relay. Double MASQUE proxy. CDN partners have deployed it.
* What changed?
* Strongly associated Datagrams with request. Removed flowID, replaced by streamID of associated request.
* Only encode >> 2 to save bits
* Carry context ID. Optional. For applications with second multiplexing layer. E.g. IP tunnels that want compression. Can use same stream ID peacefully
* Previous drafts: Stream chunks! Sequence of TLVs in data stream.
* Intermediaries had to change to QUIC Datagram. Broke the don't think about it property
* New approach: Capsules
* Frames hop to hop, not end to end.
* Capsules get forwarded without touching
* For HTTP/1 define new protocol that is stream of capsules.
* Consensus: people ok with it?
* Both work, similar
* Capsules keep data stream around
* Both extend semantics
* Architectural discussion (commence)
* MT: new end to end semantic. Would like to see more motivation for doing it. Pretty major. Nice thing with just in message or connect (connect is weird) is on top of stack if not H/3. Interested in early implementations of MASQUE. Get it working just by CONNECT-UDP, handle with protocol.
* David: Not having datagrams is the motivation. We need to proxy UDP, opportunity for efficiency. My motivation is to focus on QUIC, earlier versions supported not as first party
* MT: Fair. Imagine API only widened to send related to stream. Datagram draft does. Get capability without surface area. No new HTTP/2 HTTP/3 frames. Ability to see stream sent bidirectionally and new streams or datagrams. Far more incremental.
* David: Kind of depends on which sets of service intermediaries want to optimize for. A client deploying this doing datagram work, surgery on quic stack. Intermediary. At Google Front end speaks HTTP/3, various version to backend. With either one deep surgery. No ability to look into data frames. Less work to do HTTP/3 frames.
* If different, priorities change.
* Mike: echoing MT's point. Not sure right way to build it, might be. End to end frames intriguing. Questioning in datagrams, nothing specific, just first customer. Feels like HTTP should be the ones for it.
* Mike: CONNECT-UDP doesn't have to be satisfied by UDP on other side, could be proxy sending its on request upstream. Works same way as CONNECT. Thing that matters is saying "yes, I can deliver". Regular frame hop by hop, if it can make CONNECT-UDP upstream that's ok. Only supports QUIC to Capsules. OK to inject new frames, new request.
* Mike: Constraint on parsing data, that constraint is an argument for capsules, don't know if it can be designed around. Do other implementations? Or terminate CONNECT-UDP and serve it?
* Tommy Pauly: riff of of Martin. Implementation alone sympathetic to using data stream. We built MASQUE as layer on top of H/2 or H/3. Handles all proxying logic. For datagrams did have to expose new stream to peel out. Used to using stream to go down into HTTP layer. Yet another flow sounds like more for generic HTTP layer to implement. New semantic across HTTP, or ordinary semantic? All HTTP APIs build in? Does this need http-bis for overall implications. Why not on data stream? capsules on the data.
* David: either could work, hence tricky conversation. If people want it sure. Makes sense for HTTP datagrams doc explain how to convey over HTTP 1 and HTTP/2. Also true for Web Transport. I think widely deployed when QUIC Datagrams to intermediary, fine to reduce to H/2 or H/1 on backend. Makes sense for capsules/stream chunk in HTTP datagrams doc. Had brought to HTTP WG, but they say "new cool semantic, come back in WGLC". A lot of HTTP enthusiasts in room. Not sure room doc is in is the important part.
* Alex: QBONE experience. didn't have HTTP constraint. Own non HTTP stream. Interesting observation about capsules. Leads to question of HTTP/3 frame or data on top of frame. What is this for? Frame intended for origin server different if front end to make different decisions. Intermediary inspection is that mingling. Capsule separates HTTP vs. higher level protocol. Avoid reinventing new protocols and multiplexing on top of HTTP. IP proxying don't want another TLV and intermixed data. Capsule proposal mentally fits HTTP vs higher level better.
* David: Data in general for HTTP is request response. Message in data frames, response data frames. Here we have bidirectional flow, going to appplications. Some could be server initiated. Features that can rely on that. Kind of strange to put in data frames. Not semantic different.
* Martin Duke: Intermediaries and intermediaries. H/1 changing, yes. Boxes doing H/3 to H/1 that don't do datagram that could just take things, but drop new frame. Barrier to deployment of capsules.
* MT: Really about building on HTTP and version specific bits of HTTP. Real value on HTTP/3 because others no dgrams. But on HTTP lots of advantage. One point: flow control for control messages could become critical. Connect IP. Start transaction have routes end, commit them at once. Buffering requirements, etc.
* David: flow control: QUIC or H/2 stream sufficient, or flow control in capsule/chunks flowing?
* MT: capsules in H3 flow controlled. H/2 not. Not easy. Things get wacky: only contents of data. Problem, extension semantics etc. Preferences to extend from HTTP semantics.
* Victor Vasilev: With capsule same problem as what we had before. In theory capsules for everything. Find sending stuff in data attractive, much more clear as method gives semantics. Same with extended connect and WebTransport. In general find Martin's arguments persuasive.
* Jana Iyengar: not going to rehash, think existing data good as well. One point earlier is that HTTP kicked this over, seeing that not local, shared with WebSockets. If it is case then might belong back there as semantic extension. 
* David: no need for semantics to live there, HTTP just wants to be aware
* JI: doesn't seem that you see this new capsule a deployment hurdle. Seems clear to me capsules harder to deploy because new frame type.
* David: was working just from our implementation. Happy to give it a try with chunks and see.
* David: one question. Muliple changes at same time. Mostly focused on new capsule rather than chunks and dataframes. Happy to swich back to that. Other change: from CONNECT-UDP into HTTP datagram document. Moved when split from flow id to contextID+streamID needed negotiation, had dependency, moved down.
* Even if switched back makes sense.
* Lucas: makes things in right place. CONNECT-UDP simple doc as result.
* Lucas; but having changed, fell into trap of capsules as full frame. Probably need to treat as DATA frames. Responsible for parsing and deciding. Probably why interop entries not there. Feel some of the things people are saying, nice to make some code and crack on. Sad to have to go back from all the work, but part of the question we answered. We could do on data stream but maintain dual layer. Still have consensus even if not capsules.
* David: sounds like DATA frame, but keep stream chunks and the re-layering.
* Alex: feels like people started off being unhappy with overloading of DATA, now unhappy with Capsule. But which is worse? Three months from now will we flip again? what can we do to not flip flop?
* David: very good point. On the list quite a few people like it, now against. Did I miss a meeting? Would be great to not take 10 years, want to ship
* Tommy Pauly: To last point; this is progress. Both ways seen important then can judge it better. Cement it down on list consensus call would go forward. Stream chunks depends on what data stream means. Very method dependent. If keeping in HTTP datagram clear here are the requirements on methods that can use it and methods that can't. Connect UDP and Connect-IP, but not GET or POST. Could define in methods, maybe not. 
* David: One part of which doc goes where. Duplication not end of world. This is what a capsule data stream is, method says they use it. one difference that matters is capsule types IANA registry. One at HTTP Datagram or every method defines it own. Only distinction.
* Tommy: WebTransport? Wants to use datagram, also stream chunks?
* David: victor will take it!
* Victor: Was going to make comment, yes if Capsules over Data method has to use trimstrings. If it wants to use contexts. If you want datagram capsule have to convey universal. As for WebTransport issue is yes. Two issues solvable by something like capsule. Currently WebTransport can indicate things like session level GOAWAY. Consensus features that need stream of chunks. In march write new thing, if HTTP datagram provides will not have to.
* David: strong argument for datagram layer.
* Tommy: What connect?
* Victor: Connect with special things, allows i o work
* Conclusion: Move back to DATA frame. Look at the list.
* 13 minutes left to do 10 slides.
* Substitute for capsule
* To carry over H/1 H/2 need to layer. Some applications that want to reliably deliver get it for free
* Context e2e, opaque to intermediaries.
* Each request 62 bits
* Even client initiated, odd server initiated
* Bidirectionally
* Registration unilaterally declares a context
* Either end can close
* E.g. compression. Registering 4 with associated IP header, after a while server says I want to kick out the header. FYI 4 is dead to you.
* Example of context connection
* CONNECT-UDP draft: use HTTP Datagrams, shove it in, done!
* Issue: context ID extensibility
* How do we mix the TLVs? T's in IANA registry, L's varints.
* Encode anything: do we need that much?
* Don't need a sequence, just one for a format
* Difference would be for composeability. UDP, and then someone says "I'm adding timestamp". Someone else says "set ECN". How do you order?
* Discuss:
* MT: contexts hop by hop? David: no, E2E.
* MT: So in scenario with intermediary moving datagrams to stream don't need to understand this
* David: yes! Big feature is E2E for new extensions.
* MT: datagrams get there E2E. So why extensions, instead of new message. Thinking of small joint number.
* David: makes sense. Layering thing. HTTP datagram doesn't know format. says "datagram", no more info. if every single thing needs own register of contexts and capsule types.
* MT: combinatorial explosion, then extensible. But content ID not closed
* David: both work, have to think about it more.
* MT: every new extension point needs looking after. Don't make more, will need to look after it forever.
* David: register datagram context. Doesn't have extension, registers field. if we want CONNECT-UDP with timestamps make a new capsule. CONNECT-UDP register context for plain UDP, any new format new capsule type.
* MT: small number of constrained additions. IP compression, QUIC conn Id removal. Opportunity to simplify.
* David: will look, fewer registries good.
* Lucas: Sounds like MT's proposal much cleaner
* David: last issue CONNECT UDP and web transport thing in common
* One new method, other extends CONNECT method with protocol pseudoheader.
* Why are we doing both? Can we coordinate?
* Victor: different things with the same name. CONNECT-UDP is traditional CONNECT. HTTP endpoint proxy. Extended CONNECT means instead of proxy acting as resource, called CONNECT originally for WebSockets, proxying to real WS server, but don't have to, resource as is. That's why they use Extended CONNECT
* Kazuho: Complication with intermediaries that buffer

## CONNECT-IP Proposals

### Proposal 1: [The CONNECT-IP HTTP Method](https://datatracker.ietf.org/doc/draft-cms-masque-connect-ip/)

(Alex presenting)

* IP connection, applications to point to web, network to network, etc.
* endpoints act as routers
* Check local policies on each packet, transmit via implement spec mechanics, such as raw sockets
* On receipt do in reverse. Same local policy then transmitted in tunnel.
* To make functional, need to have IP address. Request IP, respond giving IP
* Messages straightforward
* Prefix lengths to be able to support IPv6 privacy: /64 everything works
* IPv4 everything will be max most likely
* No need for request response: server can say "here is your address first"
* EKR: note that it's required for bridging, not VPN use case. Otherwise can just NAT. Definitely need a range.
* Alex: for people using IP for this, need IP address here, 1918 necessary. Otherwise hard in kernel
* Tommy Pauly: Do think for the use case of exiting, yes UDP and connect great, but use case for CONNECT IP here. ping!
* TP: Don't care about address. Having mode where we don't need it is a valid use case. Some way to make it happen without address exchange.
* Alex: Could add. Nothing is precluded, just don't use these. Maybe add in that you want this mode.
* Personally worried about OS security. ICMP NET_RAW questions
* Need to know destinations. Need to put most but not all into it. RFC 1918, vpn itself
* Have routing messages to indicate what is available
* Endpoints can apply local policy
* Here need prefix length to advertise
* Route rejections, route reset: servers can withdraw, also don't track
* Atomic start atomic end: Convey sets of configuration need to be applied together
* SHUTDOWN: tells peer why we're telling it to go away
* Extensibility: three mechanisms: header, datagram, capsule
* E.g. configure DNS as capsule, Authn/Authz as header

### Proposal 2: [The CONNECT-IP HTTP method for proxying IP traffic](https://datatracker.ietf.org/doc/draft-kuehlewind-masque-connect-ip/)

(Mirja presenting)

* Two modes: flow forwarding and tunneling modes
* Proxy performs validity check in tunneling mode, but it is direct IP packet copying
* Flow forwarding is for a specific target, similar to CONNECT-UDP, the proxy generates the IP header
* Both modes have similar signaling
* New features for IP stability or reuse across flows
* New feature for identifying inbound(?) packets
* Open issue when it comes to ECN support for flow forwarding mode

* Ekr: how do I ask the proxy where to egress traffic?
* Mirja: If you want egress in the US, choose a US proxy
* (obvious confusion)
* Magnus Westerlund: You get an IP from the proxy, you then use that as your source address for packets that you send over the tunnel.

Mirja: the tunnel mode needs that the client be trusted by the proxy
* No route negotiation because the proxy will know where the client is able to go, this might be outside of the protocol or a property of the resource (not in the draft)
* Flow forwarding is like CONNECT-UDP and this is just extra overhead and you can do other protocols

* David Schinazi: consumer VPN, point-to-network, network-to-network; if I wanted a VPN, how does this draft ensure that you get split tunnel mode?
* Mirja: you get that
* David: how does that get signaled?
* Mirja: the client needs to know
* David: That is not how I would expect a point-to-network design to work (IPsec etc...)

* Ekr: What is flow forwarding mode for?
* Mirja: similar to CONNECT-UDP, just a little more information and you get reduced overheads, works for anything that is per-flow/per-target
* Ekr: why would I choose to implement this in addition to an IP doodad?  If I don't have CONNECT-UDP, why is this beneficial?
* Mirja: you might want to have some of the efficiency benefits and still control your outward IP, but also for non-UDP cases
* Ekr: Why not CONNECT (for TCP)?
* Mirja: Not if you want full e2e

* Sam Aldrin: do you need to maintain connection status on the proxy
* Mirja: you need state for every request
* Sam: how do you load balance proxy instances?
* Mirja: you connect to a specific proxy
* Sam: what if a server connects to a different proxy instance
* Mirja: the IP assignment for a tunnel needs to refer to a specific proxy instance
* Sam: you need connection symmetry: the traffic has to come back to the same proxy?
* Mirja: yes

* Tommy Pauly: flow forwarding seems attractive for a privacy proxy mode; unlike a VPN, I might not want a single IP assigned to my client.  The connect options allow the same IP address to be shared to provide better anonymity.  CONNECT-IP lets us do that, but it isn't a fully-fledged approach.  I think that it's worth having something like that.
* Mirja: that was a deliberate choice; this is e2e, but it is very similar in terms of usage to CONNECT-UDP

* David: what problem does the flow mode solve?  Tommy's idea sounds just like a NAT.  What use cases in our requirements does the flow forwarding mode address?
* Mirja: This is NAT functionality, but the client has control over that.
* David: If we are discussing a proxying solution, the focus should be on use cases.  We removed stuff from our draft for that reason.  The stuff you seem to care about is not stuff we agreed.
* Mirja: this is just the point-to-point use case
* David: think IPsec (opposite of tunnel mode); the e2e connection goes from IPsec client to server directly; this seems more like a consumer VPN case
* Mirja: you can connect to a specific target in an outside network, but the proxy can see the flow, which allows the proxy to apply certain optimizations
* David: you seem to be adding solutions to problems that we haven't agreed to solve
* Mirja: flow forwarding is largely redundant to CONNECT-UDP, but the differences are very small

* Tommy: Maybe we should not worry so much about process.  The earlier draft was more complete.  We don't want to lose that, but at the same time, it adds a lot of complexity that is only useful sometimes. this document can show us how to deliver valuable features with a simpler design.  you could build that with a more complex negotiation, assume that it has a NAT, and so forth.  There is value in a simpler case that allows for other IP protocols.  There is value in having the proxy do name resolution to reduce latency.  maybe those aren't in the requirements doc, but they are important for real world uses, especially for short-lived cases (ping this host).  let's keep the good bits of the other, but we shouldn't dismiss the benefits of this.  flow forwarding is the main use case that I want to adopt
* David: wasn't suggesting we dismiss
* Tommy: it seemed like that
* David: *throws process on the table*
* Tommy: this is a way to meet at least one of the requirements, but also efficient
* Mirja: would like to ensure that we end up with a single document
* Alex C: In this use case, we seemed to have missed it, but it is different than what I understood (?). A lot of that short-lived stuff happen in kernels. Want to understand what the requirements are.  Our design manages complexity; I think we can deal with these use cases with a simpler one-shot mode.
* Ekr: if I just want to ping, I can just do that with a single round trip?
* Mirja: I guess so ... ? (Tommy's use case)
* Ekr: Do I have to get an IP address if I don't care what it is.
* Mirja: In flow forwarding mode you can send data out directly.  Maybe also tunnel mode.
* Alex C: what is producing the packet?  The stack generally requires a source address.  Maybe with special logic at the proxy you might not care about the source IP.  That's more like CONNECT-PING than CONNECT-IP.
* Ekr: Maybe not caring about source IP isn't important; it's the extra RTT that matters.  Tommy seemed to care about getting an unlinkable IP addresses, which the "classic" setting doesn't provide.  That seems like an attractive property.
* Ekr: The chairs need to do some work here.  I don't want the next 6mo with each proposal working independently on copying stuff from each other.  Can we find a way forward that doesn't involve a beauty contest, but that's a chair job.

===== TIME =====

Eric Kinnear: we'll look to finding a unified solution; continue on the list.  No time for prioritization, sorry Lucas.


## New Work (as time permits)

- [HTTP Datagram Prioritization](https://datatracker.ietf.org/doc/draft-pardue-masque-dgram-priority/)

(No time remaining)