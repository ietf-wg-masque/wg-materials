# MASQUE @ IETF 113

Monday, March 21, 2022, 13:30 - 15:30 (UTC), Monday Afternoon Session II

Location: Grand Park Hall 1

## Administrivia (5 minutes)
  
Interop at Hackathon: fairly successful

## CONNECT-UDP and HTTP/3 Datagrams (60 minutes)

Consensus from the interim now reflected in the drafts.

Overview/reminder of the changes that went in.

Extensibility/demultiplexing is/was one of the most controversial aspects of specs. Moved it out of H3 datagram and into higher-level docs such as CONNECT-UDP.

### HTTP/3 Datagrams

We have no more issues in H3 datagrams, think it's ready for WGLC.

No comments from anyone in disagreement.

Mirja: should there be one draft that uses extensibility (but that's CONNECT-UDP, not DATAGRAM)

Chairs to start WGLC right after this.

Eric: is there anyone who thinks we shouldn't progress H3 datagram to WG last call, please speak now? 
(resounding silence)

Tommy: ready for WGLC; make sure httpbis also reviews

Eric: yes we'll certainly do that

### CONNECT-UDP

See - in the wire image the context ID is now after the quarter stream ID.
To make use of non-zero contexts, we state that conext ID registration can be used. We don't define the specifics about how to do registration. Are we happy with this? Does the WG agree with this?

Ben Schwartz: Iam one of the people that expressed some concern about this. I wrote a draft to try and try this. It included a lot of boilerplate and I found I left things out. Concerned we have different extensions that miss important things like error handling. Reuse would be good. Coming up with some extensions that do things right and act as a template would be useful.

Ted Hardie: headers or capsules could be used. But pick one now and define it, while mentioning the other. State that this has worked out the issues that Ben mentioned. If somebody needs the other thing, they can base it on the base spec. If I were picking one, I would pick HTTP headers. You potentially won't be able to put this off for long. So I would do it now. In a different document would be ok.

Dave S: I would prefer to put this in another document, but starting right away sounds great.

Mike Bishop: I feel somewhat hypocritical. H3 says figure it out but it suggests using a setting. I don't know how you can handwave it away, you don't have a setting. A seperate document would be fine but we should start it now.

Dave S: in equivalence to settings, we have headers. Since capsules can be used after request or response, we can enable them via headers.

Marten Seemann: I'm wondering how extensions compose. You mention ECN bits. In the draft are timestamps. So one context ID for ECN, one ID for timestamps. Do I need another ID for both ECN and timestamps?

Dave S: (yes, in more words)

Martin Duke: request/responses are pretty obvious but the downside is you can't use this in the first flight. I don't know if the server can accept them.

Dave S: I think we have a solution for this already in the draft, I'll need to check. Previously known at 0-rtt extensibility. Client sends in first flight, the server can just ignore unsupported context IDs.

Martin Duke: that sounds ok, but it can give you a 1-RTT penalty.

Dave S: there used to be something in the draft. As a design team we decided it was too complicated and removed it from the draft.

ekr: (hard to hear in the room) on the topic of 0-rtt extensibility, unless you have mandatory extensions, the most you could possiblydo is announce them in the first flight. Perhaps you could have extension persistence. something somthing critical path

Dave S: if its ciritical to have an extension in the first flight and that is not supported the datagram that uses it would be lost. You probably would want to avoid using such things in the first flight or pay the penalty.

ekr: You can't use extensions in the first flight if loss would be a big fail.

Dave S: People are OK with this draft not defining this extension, but doing it in another draft.

#### Issue #58 Well-known prefix for default URI template

Should the URI template use .well-known, or a shorter path?

Ben: I think using .well-known is the correct place
MT (in jabber): +1

Dave S.: /.well-known/masque/udp/{target_host}/{target_port}/ OK with everyone?

Rajeev RK: some proxies have special handling of .well-known addresses. Do you think this might interfere badly.

Dave S: I don't know if I have a great intution of this. Gut feel is you probably need special handling for MASQUE so perhaps this aligns with what they do.

Rajeev: Perhaps just adding some form of warning in the draft text to note the possbility would be good.

Dave S: sure. Unless anyone objects. I'll make a PR. 

#### Issue #57 HTTP/1.1 Method for Upgrade

Dave S.: CONNECT or GET? httpbis didn't care. So let's use GET to avoid deployment concerns. Any thoughts or opinions?

MT: Cacheable by default, could cause caching errors

Dave S: caching is a real concern

Lucas: It would also exist for Websockets. Asymmetry for H1 vs 2/3. Caches really should look at the Upgrade header. CONNECT is just weird and will be a mess in 1.1. But whatever.

Dave S. A box that doesn't support this will probably just result in a cached 404.

ekr: What is the scenario that creates a problem?

Dave S. Say the proxy doesn't support MASQUE. Least dramatic way for it to fail?

ekr: Then don't do that!

Rajeev: Edge devices have different security rules for GET and CONNECT. CONNECT is a clearer signal to those devices, even if Websockets isn't doing that.

Erik Nygren: Multi-tenant services might like POST better?

Dave S: I don't have a 3 sided coin. Speaking for me, we are probably never going to support the HTTP/1.1. version. I don't care much for the answer but lets pick one.

Dave S. objections to POST?

ekr: Yes. We have a thing for this: CONNECT. Why overload GET or POST?

Dave S: if something sits in the middle they might handle things differently; might not control their own backend where they run 1.1.

MT:  I think what I'm hearing is that people are saying CONNECT plus Upgrade, we don't know what will happen and it might explode
Anything other than CONNECT is OK.

Mike Bishop: there's a different between CONNECt and other methods. Defining a new method might be nice but that feels like a lot of work. We could progress without a new method. If we want something that is not a hack, we need a method.

Ben S. new method: Extended CONNECT. Upgrade path is nominally optional for server. CONNECT without host/port and ignored upgrade will be a malformed request.

ekr: MT persuaded me: anything but CONNECT

David S: Don't care; just pick one

Lucas: CONNECT is bad because its spec says what unsupporting servers should do. Now you can't just react based on the METHOD. A no-op if you handle Websockets

Tommy: I've been convinced we should just go with GET. I'm not convinced the overhead of a new method is worth it. We are not seeing compelling evidence that the HTTP/1.1 approach will be widely used.

Dave S: agree

Martin D: I'm leaning towards GET. Seems like a bikeshed, maybe we can take it to the list

PHB: I'm just wondering why you are doing HTTP/1.1 at all. It's complicated enough already. If people need to downgrade just let them decide how to do it on their own.

Dave s: was clear consensus years ago to do 1.1. GET opponents seem to have acquiesced. So move forward with GET. Anyone can't live with that? 

Dave S: We have proposed resolutions to all open isses. Are we ready for WGLC?

Eric: WGLC seems appropriate after these issues are closed.

Dave S: WGLC also motivates people to read the drafts.

## CONNECT-IP (15 minutes)

Tommy Pauly: I'm sharing our other adopted draft - collaboration between myself and folks at Google and Ericsson.

MTU and Error handling are the open issues. Need more interop.

### IPv6 and MTU 

https://github.com/ietf-wg-masque/draft-ietf-masque-connect-ip/issues/45

For MTU, what to do when overheads drive pkt size below 1280B? Fail or do a hack (like using capsules). What approach should be take?

Ben S: core spec should note as a problem, and do nothing. solve it as an extension.

Tommy: I kind of interpret that as the last option presented on slide 4

Ben S: yes that's a fair representation

ekr: if you declare yourself as an IPv6-carrying tunnel then you have to carry IPv6. I think the question is between these options you pose, the first is like maybe you can imagine doing that. "I only support v4 not v6" is kind of ok but gross. The last point is really an implementaion point. If someone asks for v6 and you can't support 1280, then you should reject it. The only mandatory advice is you must reject but also provide some advice on what to do.

Tommy: that sounds like a combination of some of these options

Lorenzo Colitti: Problem if link is 1280. Is it a downgrade attack? Need a fallback mechanism.

Tommy: define in another document that would support how to avoid this kind of adversarial situation

Rajeev: (inaudible in the room) .. allow things to work, but make an explicit signal to allow notification that fragmentation is happening
e.g. fragmentation with a warning. Allows endpoints to make a decision about how to handle the situation. A running session might be degraded but it should not fail.

Tommy: would you be fine with defining this fragmentation reliability behaviour as an extension?

Rajeev: that should work. I believe that having a flag would be useful. But the default should be try to ge tthe traffic throguh as best you can. If someone realyl needs a guarantee for performance, they can handle that

Tommy: I'd prefer a kind of opt-in. Also applies to CONNECT-UDP

Mike Bishop: try and get it through. SHOULD have a large enough MTU on the connection without tricks.
 but use capsule if you need it
 
David S:  my thinking is that a network with an MTU so low, it is borked. I like the last approach, it is really simple and in practice I think I would implement. Other things seem more complex. Concerned about time to spend on complex things. Let's keep it simple.

Gorry: We went through this when we did DPLPMTUD. That handles lots of cases, this is the final case of what do you do when it doesn't work. Large value at the start is good. The paths can change remember, MTU can change. Simple is best. 

Martin Duke: Strikes me as not a new problem. IPv6 tunnels have it. Not an intarea guy but maybe there is some wisdom that exists already. This is CONNECT-IP but someone mentioned CONNECT-UDP, and we just said we closed it. This problem is not just a CONNECT-IP one.

Tommy: comment on solutions at other layers of tunneling. A CONNECT-IP stream is a bit more stateful.

David S: this problem is specific to CONNECT-IP becuase of the minumum 1280 size for IPv6, therefore it might not apply to CONECT-UDP

Tommy: clearer example, a network only allows 1200. Any you tried to tunnel QUIC, which then could not satisfy the minumum 1200 QUIC packet size.

Martin D: (missed words) agree

Mirja: I'd like to see a solution that uses capsules. We have capsules. This is just like IP fragmentation

Lorenzo: I'm going to disagree with myself. Connect UDP has this problem. All more complex than a fail. Either fail or be simple by having one way to fix it, which means use capsules. Do one thing all the time. Maybe just fragment.

Tommy: capsules are carried on streams so they are reliable. Fragmentation has lots of failure modes so capsules have an advantage.

ekr: this is sort of an issue for UDP as people are saying. But every UDP system I am familiar with has to handle packet sizes. If you don't get teh right size you're at risk of things filaing. Capsules are fragmentation, whether there is retransmission or not. The question is, can the system ship at all without support for this. Which leads on to another question. (missed it in the room)

Tommy: agree fragmentation over capsules is fragmentation. That behviour is exactly the same as if you fell back to H2, which only supprots using capsules. If I had to handle that, I would already have a way to deal with things.

ekr: in which case you could just say use H2. When things fail you have to design for it. DTLS says start at 1280, if that fails 2 or 3 times failback... These appraoches add 4 or 5 rounttrips before recovering, is that the kind of system we want.

Mirja: we could try something with unreliable datagrams but we have capsules. I don't like much suggesting to fall back to H2.

Rajeev: you could try to define these various retry approaches but the endpoints don't get the visibility of it. If we define something too simple are we pushing the complexity somewhere else.

Lorenzo: Always do everything in capsules. 

ekr: (inaudible)

Tommy: if you had an event like migration maybe you could know

ekr: we didn't need to define this kind of failure mode for H3. If we want people to avoid weird mid-connection hiccups, mereley saying use capsules won't prevent that. We need a complete answer. Address unknown topology shifts. If it's a known topologiy shift, just to path MTU discovery. Retransmit timers are pretty fast.

Tommy: we drained the queue, we don't have consensus but we had a lot of good input. Hopefully we can come back with a proposal next time along with implementation experience.

### Error handling

https://github.com/ietf-wg-masque/draft-ietf-masque-connect-ip/issues/43

Tommy: any opinons?

Rajeev: use the approach liek you would any other network: ICMP

Gorry: 2 ICMP messages are best: PTB and TTL.

Tommy: multiple people asking for ICMP and making it friendly

Mirja: where do you send ICMP, inside the tunnel?

Tommy: in the tunnel.

Mirja: but then it's not visible to anyone

Tommy: say that the deployment uses a virtual interface and the application doesn't know aboutthe tunneling. You could pass the ICMP on the the app and they could deal with it.

Mirja: seems like a dependency that is not necessary to me

David S: were building a link. The Internet Protocol says to use ICMP. We don't need to define anything special here. Implemenations guidance seems useful, we don't need to defien anything new using capsule.

Mirja: I agree. Maybe a benefit to silently drop, the risks maybe don't apply here. Maybe we need a feedback mechanism in our protocol?

Tommy: Sounds like saying that's option 1 (slide 5) and option 1 is bad.

Mirja: if you have a reason to drop use ICMP. If you provide a CONNECT service and you can't support what I'm asking for, then you should tell me.

Rajeev:  "I was saying that Silently Dropping packets should be strongly discouraged in the draft text, since the IP router tunnel use case is more limited, and many of the good reasons for silent drop in respect of general purpose IP routers. Here, since both endpoints of the tunnel are more tightly negotiated, ICMP signaling is just more polite.""

ekr: struck by what David said. Standing on hundreds of RFCs. Hoping we can defer to other documents. Can we focus just on topics that are distinct to MASQUE? Punt other stuff to already defined documents, that might need some research but its is possible. 

Martin Duke: read the spec, I don't understand the case where a client could pick an invalid address or route

Tommy: client can learn the correct value but the contents of a datagram are an IP packet that could contain something invalid

Martin D: just close the stream then. In QUIC we are strict, we can be strict here. Kill the stream if not the connection.

Tommy: there are other cases you could receive ICMP, beyond these cases

Martin D: I see no downside to killing the stream

Mirja: if the source address is wrong, you could just rewrite it. Maybe we should just specify things to avoid the problem? IF you have the other case when a detination is not allowed, just dropping it could work but having a signal to indicate that would be useful.

jabber: Phillip. Don't event a new method. ICMP is a good step. Protocol error that turns into ICMP at the client makes sense

ekr: two networks tied together use case. If there are a bunch of devices all using a tunnel, breaking the connection due to one bad device is bad.  steal from giants

Martin Duke: I concede the point.


### IP Proxying
Support for HTTP


