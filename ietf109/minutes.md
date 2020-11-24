# MASQUE at IETF 109

* [Jabber chat](xmpp:masque@jabber.ietf.org?join)
* [Jabber log](https://www.ietf.org/jabber/logs/masque/2020-11-20.html)
* [Meetecho](https://meetings.conf.meetecho.com/ietf109/?group=masque)
* [Minutes](https://codimd.ietf.org/notes-ietf-109-masque)

## Friday, November 20, 2020 (UTC)
**9:00-11:00 (UTC), Friday Session III**

[See this time in your local timezone](https://www.timeanddate.com/worldclock/fixedtime.html?msg=MASQUE+-+IETF+109&iso=20201120T09&p1=1440&ah=2)

### Housekeeping

* Scribe Selection / Note Well
Note taking: Robin Marx
Jabber scribe: Lucas Pardue

* Agenda Bashing
**Tommy Pauly**: Less whiplash about conversation to re-arrange agenda so IP and UDP are treated back-to-back
**Christopher Wood**: seems like a good idea, but it's not unanimous in the chat, so let's stick to what we had
**Alex Chernyakhovsky**: will present slides for Dallas McCall, he's unavailable

* Interop Results - Lucas Pardue
**Lucas Pardue**: interop matrix. requirements taken from doc alongside implementations that have been doing interop. [see online](https://tfpauly.github.io/quic-proxy/interop). Tommy can talk to how this is generated.

**Lucas Pardue**: Direct Connect-UDP method was tested. Lucas recently added DATAGRAM support and forked quiche to build MASQUE on top of that. During hackathon, setup public test server and interoped with Tommy. Lots of green already. 

**Lucas Pardue**: For QUIC-aware proxying, much less green, more complicated. Still feels interop was success. Tunneling h3 in h3 connection is already nice. Identified some gaps such as H3 SETTING which I forgot. Quiche interop server will remain online, though be aware of access control because can be used to proxy to any origin, so manually sync with Lucas if you want to test.

**David Schinazi**: We (Google) aren't on the matrix yet, but other work prios got in the way. But half an hour ago we got Connect-UDP working, so we're getting there. When you already have an implementation that supports H3 and DATAGRAMS it's pretty straightforward. Just some changes to how fin-bit was handled. 

**Lucas pardue**: We needed to rework app code to consider recursive QUIC connection. That was hardest part for us. For browsers that already have that model for normal HTTP proxying it should indeed be more drop-in replacement.

**Tommy Pauly**: I'll post more to the list on how to update the interop results. Basically: PR with json that identifies your results and then it annotates itself. 

**Lucas pardue**: Will take something to the list about identifying UDP and TCP servers to further test this, but will take it to the list. 

### Presentations


### Working Group documents

#### CONNECT-UDP (draft-ietf-masque-connect-udp)- David Schinazi (30 minutes)
[Draft](https://tools.ietf.org/html/draft-ietf-masque-connect-udp-00)

**DS**: I confirm that I am a Taylor Swift fan

**DS**: Big change since last IETF: draft was adopted. Rely on H3-Datagram draft (draft-schinazi-quic-h3-datagram). Datagram-Flow-Id is used to map "connections" to a given datagram flow. 

**DS**: (next slide)It's a bad idea to give chairs write-access to slides(next slide)It's too early in the morning for this(next slide)

**DS**: Adoption inversion, because H3-Datagram draft isn't adopted yet... How to resolve? Flow-Id is in Connect-UDP and in H3-Datagram. H3-Datagram also has SETTINGS. QUIC-Datagram has DATAGRAM frames and how to negotiate them. 

**DS**: Flow-Ids are needed to support multiple CONNECT(-UDP) on a single H3 connection. Two options: a) re-use HTTP request stream ID = simple but uses longer varint encodings. b) current approach with Datagram-Flow-Id = more flexible (e.g., many-to-one mapping is already used, see later). Open queue for opinions. I prefer option b)

**Lucas**: I raised an issue on this on one of the drafts. For example: send Connect-UDP with stream-id 0 and flow-id 0. In most examples, these are the same, feels weird. Might be because we start from a simpler case, so people gravitate to that. Depends on how you present it maybe? Haven't really talked about quic-aware proxying yet. Soon as you understand that, you know option b) is useful. So I'm in camp b)

**David**: Good point. Let's hear from rest of the queue. Won't take definitive decision

**Tommy Pauly**: We should keep b) regardless of the other extensions. Having done the implementation myself, it was simpler to handle the logic for the Connect-UDP layer. This explicitly needs to open up from datagram connectivity with QUIC. API can just do "want to open a new datagram flow with this ID" which is easier than the higher entanglement from getting a stream-ID assigned internally. Essentially: too many entanglements to how streamIDs are assigned internally. Better to have higher-layer control over this.

**David**: if someone were to build a different application over H3 that did some stuff over unidir streams in H3, they then could still have a way to do this. So more extensible in those terms too

**Mirja Kuhlewind**: Agree b) is more flexible. However, depending on how you assign the flow ids you might need some conflict handling. So needs to be further specified. 

**Eric Rescorla (EKR)**: Longer varint is a red herring, esp if it's just 1-2 byte difference. We do not need re-using flow-ids, we don't need manyto-one mapping at this point. So using a) is simpler and has other advantage that you can't see incoherent things (e.g., 2 connections with the same flow-id and things collide). With a) you cannot accidentally re-use flow-ids, so don't need to check for it. 

**David**: Disagree. Having implemented this, it didn't introduce complexity for us. On the proxy you need to keep a map for flow-ids to a bunch of state. When using CONNECT-UDP, you just check that hash map. If something exists: problem. 

**EKR**: True, but it's an ew thing you have to do and can get wrong, so it's not free. 

**David**: Analogous to Push-IDs. Own namespace that is distinct fro mstreams and that works

**Victor Vasiliev**: I'm suggesting a) because streams are only thing in QUIC that exist permanently on top of connection. So all Datagram flows will be naturally associated with some stream. Otherwise what would they be associated with, because ntohing else exists. I think one-to-one is enough for most things and now convinced we need manyto-one

**Lucas**: Connect-UDP is defined to be used across all HTTP versions. So in proxy-chaining case, we use the datagram flow id to confirm to the masque client if it can use Datagrams or not. We need that signal either way, so we'd need to use something else. 

**Tommy**: How much of the question of allocation has to do with Connect-UDP vs how we're doing Datagram Flow IDs in general. Seems like these have broader use cases not just tied to one request on one stream. Or would this proposal change all Datagram Flow IDs in H3?

**David**: Good point, next slide. This is a general question of general Flow IDs. For example, for WebTransport Datagrams are also needed. There, the proposal is to use a). So we need to decide on something for H3 at large, otherwise won't be able to multiplex on the same Datagram flow, which we need to be able to do from an HTTP semantics perspective.

**Tommy**: I prefer this is separately negotiated like in b) then. If we don't, we eliminate possibility of requests in the future that need to open up two datagram flows. We don't have this use case yet, but don't want to have to come up with a separate flow-id scheme in HTTP in the future if we limit it too much now.

**David**: (next slide) Connect-UDP should stay in current draft. However, flow-id handling should be done in a more general fashion. Should be adopt draft-schinazi-quic-h3-datagram in MASQUE? Then we can put flow-id negotiation to that draft.

**Martin Duke (MD)**: Bit concerned about ECN whether or not it's in the Connect-UDP draft. Sad if only way to use IP proxying to use ECN for QUIC over MASQUE. Whether or not there is another draft for this or not, implies sort of per-packet feedback which needs a change to the DATAGRAM frame. Not sure there's a way to separate the concept of DATAGRAM to separate connection on top. 

**David**: Agree there should be a way to do ECN as an extension to Connect-UDP. You could imagine saying doing 2 CUDP requests with same flow-id, and here is a separate one for ECN. So you'd need to have many-to-one mapping for that, so makes the case for b)

**MD**: indeed cleaner than having another field in Datagram, so agree with that. 

**David**: So in which wg should which draft be in? 

**Christopher Wood**: prior discussion between chairs said to move it to MASQUE

**David**: So getting some consensus that we should move flow-id stuff to H3 Datagram and then bring that to MASQUE for adoption.

**David**: (next slide) Let's talk about open issues. #1 and #3 about chaining multiple proxies. Was skipped previous IETF, so let's do it now. Typically there are multiple proxies on a path in larger companies. Not all will use the same HTTP versions, so not all will be able to do DATAGRAM. Can be resolved by doing Datagram-Flow-ID hop by hop, but hop-by-hop is no longer in fashion

**EKR**: Don't understand how you get into this hole. These proxies are operated by the same person? 

**David**: True. Just wanted to know if this is an ok outcome

**EKR**: just don't. If this is your architecture, ... _(didn't quite catch that)_

**David**: Fine by me: don't have use case for this, wanted to give others way to speak up. 

**Mike Bishop**: Connection hop-by-hop aren't an option in H2 or H3 at all. Other pice is that Connect and Connect-UDP is mainly about "make this happen, I don't care how". Maybe this is an argument to not use a separate header. Of you have to put it in definition that it needs to be hop-by-hop. Ultimately, it doesn't matter because asking for UDP connection and it will ask next proxy for UDP connection as well. Client doesn't have to know.

**David**: True. More about providing guidance for proxy operators on how to do this. Maybe it's enoguh to say "if you have split archtiecture, there are dragons" and leave it at that.

**Mike**: seems good

**Martin Thomson**: Other scenario is client talks H2 to proxy and proxy talks H3 to backend. Proxy doesn't know about Connect-UDP, passes it on to server. 

**David**: if H2 on first hop, won't happen, cause can't do UDP

**MT**: So what about client-h3-h2-h3... if these just forward the headers, everything breaks, because things aren't getting through H2 proxy. Solution is to use a SETTING and make sure other side confirms this and you never go beyond one hop. If you happen to be sending this over H1, then you can put the flow-id in a header and it won't go further. Then you have a clean solution, where you can't skip intermediaries and make sure all proxies in the chain support datagrams.

**Christopher Wood**: We have time for all issues

**Tommy**: Agree with Martin. Already have a setting for supporting H3 Datagrams. Would be useful to analyze this situation if first H3 hop doesn't know about Connect-UDP, it probably also doesn't do that setting .Wonder if we can couple these together (no datagram without Connect-UDP).

**David**: Would have to put both in the same draft. I prefer having a second SETTING for a cleaner separation. 

**EKR**: IIUC Martin's scenario has a chain of client-H3-H2-H3 and connect-udp to H3, goes on to H2, but don't understand how that can happen, don't understnad the problem. 

**David**: connect-udp comes in. H3-H2 proxies don't know about it, just forward it through. Only the H3 backend knows about this and echoes it and thinks it works. But then H2 proxy would receive datagrams because don't know what to do with it. So they just drop the datagrams on the floor.

**EKR**: So, it's simple: don't construct your system this way. All these scenarios just seem non-issues because you shouldn't deploy these

**David**: Two solutions. Just say: don't do these things. or what MT said: use a SETTING, which breaks the H2 interaction, so it wouldn't get through. Does have some caveats with updating H3 settings in the middle of the connection (which you can do over H2), need to discuss later. 

**David**: (next slide) #8 need a target URI because HTTP semantics. scheme-authority-path. This has an exception for CONNECT though, but it's technically incorrect to do this for Connect-UDP then. Found we had a bunch of code that does validate there is a scheme and path is just a single slash. e.g., udp://target-host:port

**MT**: are already registration for udp://, so would need another string

**David**: ok, so connect-udp:// then. Shouldn't spinbit exact scheme today. 

**MT**: not sure we appreciate the amount of work we need for the IANA registration for a new scheme, but it'll be fine eventually. Talk to Ted Hardie

**Alex C**: if it's going to be difficult to get one, should consider if we need 1 for all of them or just 1 for each.

**David**: oooh, could just call this connect:// and apply it to CONNECT

**Alex C**: or could use masque://. I think it should be coherent across things instead of separate.

**David**: seeing lots of support for masque://, maybe we should just take that. 

**David**: (next slide) #11 Have a clear resolution for this one. Security issue. In CONNECT, when you ask proxy to connect to server, it waits for SYN-ACK. Doesn't let you send anything to the server until that has been received. prevents some amplification vectors. UDP doesn't have this... should we fix this? Do we care? Simplest solution is rate limit until we get _a_ response. 

**EKR**: SYN-ACK opens connection, but real event is that proxy does TCP semantics control. Not here, because proxy doesn't examine UDP data. So rate control will stall. Since QUIC servers will accept connections ... _(didn't follow here)_ difficult to do with QUIC because proxy can't really see what's in packets from server, so can't check it's valid

**EKR**: not sure how to solve this. In typical case, server would penalize single client, but here would penalize entire proxy potentially. Is a problem we need to deal with more generally in manageability of entire system. 

**David**: If we take step back, there is DoS, but also others. E.g., using CONNECT today to deface a wikipedia page, in some countries in the EU they'll go after the proxy company from IP logs. So solution would be to not let people you don't know use CONNECT / CONNECT-UDP. But if there is a technical solution, that would be nice

**EKR**: question of scale. If proxy has millions of users with 1 bad egg, you can deal with it. In the wikipedia example, it's a longer timescale

**MT**: Two parties that want to confirm permission to send. Client would verify that the server wants to talk to it before sending much traffic. here, proxy wants to independently verify that origin wants to talk to client. That's challenging in QUIC, because much is encrypted and/or need deep QUIC understanding on the proxy (e.g., decrypting server initial)

**David**: Would be extremely sad that deployment of MASQUE would cause QUIC v1 to become ossified. One suggestion from Olivier Bonaventure was about ICMP. What do you do on proxy if receive port unreachable on srever? Kind of helps solve this problem to some extent, but QUIC server won't send ICMP, so... never mind. need to think about this more

**MT**: do have experience with TURN, so that might influence conclusion here. 

**Jana**: Trying to figure out how you can solve this -at all-. Best you can do is adding high-level protection. e.g., circuit breakers for UDP are already in use. If sending too much stuff too quickly that comes into effect. It's not a new problem, but different from CONNECT over TCP. Can't solve this as a protocol mechanism probably, more at deployment level. Suggesting mechanisms to employ would be useful.

#### IP Proxying Requirements (draft-ietf-masque-ip-proxy-reqs) - Dallas McCall (30 minutes)
[Draft](https://tools.ietf.org/html/draft-ietf-masque-ip-proxy-reqs-00)

**Alex Chernyakhovsky**: taking over for Dallas McCall

**Alex Chernyakhovsky**: Recap of unchanged requirements from previous meeting. 3 general use cases. 1) point to point. Tunnel between two endpoints. container networking on same machine 2) point to network. More like traditional VPN. 3) network to network: site-to-site VPN. Connect different branch offices together. Also useful if separate devices can't themselves connect to VPN.

**AC**: Security requirements. Only run over QUIC/TLS. Should be indistinguishable from regular HTTPS Web traffic. Should have a mechanism to authenticate clients at servers, though don't want to specify too far. Endpoints need to have clear crypto identity.

**EKR**: Typical setup for this would be with CONNECT-TCP over H2 that you run over TLS for the first point. Server crypto authenticates over TLS layer and client authenticates at application layer (e.g., password). Point is about identity. Clear that server needs to be identified, but not necessarily for client. 

**AC**: Mostly a matter of wording. Agree username+pass should be acceptable for client login. 

**AC**: (next slide) Session requirements. Establishment. Should be able to just forward IP packets in their entirety. Need to be able to multiplex independent multiple IP session over a single HTTP connection. Need to support both H2 and H3, though H3 is preferred with H2 as fallback (e.g., Cisco has a product that does this). 

**Mirja**: On Proxying IP packets: need more discussion about the "unmodified" part. e.g., header compression

**AC**: don't think MASQUE should do any changes. Any transformations should be done as an extension

**Mirja**: Disagree, so that's why I feel we need more discussion

**David**: I agree with AC that it should be extension. In terms of design principles, we should have connect-UDP and IP proxying as simple as possible as basic solutions, but with extensibility. Doing this as extension is much more flexible (e.g., swap out extension for later, better extension).

**Mirja**: If you don't send IP header, you need extra signalling, but that's identical to Connect-UDP because you don't send header there either. Since we have CUDP already, simple to re-use that. If you think about the VPN use case, it reduces complexity. For the network-to-network case it's more difficult, but for VPN it's easier

**AC**: Disagree. If you remove IP header, network-to-network is not longer possible in my opinion. We're just saying you should be able to do full IP forwarding, but not saying you should always do that and just that. 

**Mirja**: Agree it's different use cases and we need separate solutions for that. 

**AC**: don't understand why you want to remove that redudancy. Traditionally VPN is implemented at IP layer. If removing IP headers, it's not longer a VPN

**Mirja**: it's an optimization to remove IP header overhead

**AC**: it adds complexity. Don't understand why this would be needed to do it differently from normal VPNs here

**Mirja**: by just letting proxy handle complexity, don't need to do this in the network. _(not sure I wrote this down correctly)_

**Eric Kinnear**: let's move this to mailing list / github issue. Let's try to frame the requirements and use cases, rather than in terms of solutions.

**EKR**: Do we need to support site-to-site? Then yes, you do need IP header forwarding or it won't work. If we don't do that use case, we don't. If you do, ... _something about dr who_..., Core question: unless someone thinks Mirja's proposal should be the -only- mode, we clearly need the wider option

**Tommy**: Agree we need to support full IP header forwarding. But shouldn't limit requirements doc to -only- do that. Should be allowable in the protocol that it should always be done. 

**AC**: (next slide) more requirements. Need (partial) reliability, e.g., for session establishment. Need bypassing of flow control to enable end-to-end principle. Need load balancing (use multiple streams to improve throughput)

**Magnus Westerlund**: load balancing: mostly focused on having multiple QUIC connections to not be limited by proxy performance in individual cores? 

**AC**: one example. Others would want to procss separate streams/datagram flow ids on separate cores. So should at least not be precluded.

**MW**: should need more feasability research

**AC**: when saying flow detection, I don't want to specify in this document how to map individual flows to QUIC streams or Datagram Flow IDs. It's mainly important for proxying that we just have the ability to have separate flows.

**MW**: We need clear indicators that solutions exist that this can actually work. Worry about complexity about solving this.

**AC**: Talking about capabilities here, what we want to allow, not what implementations MUST do. Mainly guidelines about what should be -possible-. 

**MT**: makes no sens to me. If we send IP packets. should be in datagrams, not in streams. Why would streams be the unit that allows division between CPU streams. 

**AC**: maybe terminology problem. Chose stream as an editing error because want to do proxying over H2. 

**MT**: still not seeing how that's relevant. Can see requirement for multiple connections, but rest seems too much for me.

**AC**: this isn't about multiple connections, but about multiple streams in a single connection. Have experience devleoping this for gQUIC.

**MT**: happy to see data on this, still not convinced this is important. If there are performance gains to be had by adjusting protocol, we should do that, but not sure that's true.

**AC**: maybe fix is to not put it under guidelines of load balancing. Hpapy to scratch this requirement, as it seems controversial.

**Jana**: agree with MT, rephrasing: should be have something as minimal as possible. To that extent, the load balacing requirement could be an extension. Also don't understand how this exactly yields performance benefits. Shouldn't be part of core requirements

**Eric Kinnear**: We built-in this ordering structure to how we wanted to approach this work as a group. With defining requirements we don't want to describe the solution we think we're going to implement. Our main task right now is to find minimal set of things it should do make my use case deployable. Conversation that we're trying to have then is : which use cases do we want to support, and which requirements do those pose on solution. NOT what are the requirements that describe a current solution of any form. Not easy to do, so thanks for endeavouring to do that. But let's keep it grounded in a use case, not in current deployments.

**AC**: Agree, and agree that load balancing is least grounded in particular use case

**AC**: (next slide) out of scope. No addressing setup. No NAT. No IP packet extraction.

**Mirja**: Can't avoid talking about NAT. Use case where client wants to hide address fro mserver. Not too complicated, but need to talk about that.

**AC**: Agree. What is out of scope is mechanism how IP is selected. Other component will just assign IP, question of policy, doesn't require MASQUE protocol changes.

**Mirja**: usually proxy has IPs it can use. Would like 2 sentences on this in the draft. Need some signalling to give client info of what the out-facing IP is. Maybe want to add a requirement that this is possible.

**AC**: let's revisit later, think we can already do this in current proposal. 

**David**: maybe there's a misunderstanding. Main question is how do we proxy IP packets : how to do ESP part of IPSec? What is the wire format? Maybe a little bit of how do you communicate addresses and routes over control channel. none of IPSec mention NAT because they pretended it didn't exist, but doesn't need to be in these documents. What's out of scope is how you pick your one IP or if you pick a NAT, which NAT will you use. These will probably be on the same box as the MASQUE server, but not integrated in MASQUE

**Mirja**: Also the case where client wants to influence how IP is assigned (e.g., wants fixed IP)

**AC**: (next slide) Addressing example - Point to network _(missed some explanation here, see slides/recording)_ 

**Mirja**: don't understand where you need point-to-network reachable adresses

**AC**: office VPN, don't want to proxy the client's entire ..

**Mirja**: ...
**AC**: ... could be anything as broad as entire internet, or only specific range.

**Mirja**: could also get this info out-of-band or hard configure

**AC**: think it's useful to have this in protocol. Simpler clients. This is a capability, not necessarily everyone has to use it this way

**Eric**: let's take this to an issue

**AC**: (next slide) Point to point. Trying to access individual range. Network to network: inverse case. Client says I let you reach one range, server says: I'll let you reach another

**MW**: This is much clearer now. To avoid ... as long as masque server offers what's in local routable domain, you could avoid those aspects ... 

**AC**: I think this is out of scope. Mianly talking about protocol being -able- to do this. ... This is about a signalling mechanism

**MW**: requirements is signalling, security aspects in general case is a problem. For secure deployment, you tie the scope to particular use case or you have to implement the particular mechanisms

**AC**: don't think that follows. Server isn't obligated to ... merely advertisement of what can be reached. ...

**MW**: Comes down to point later in sec considerations and actual deployments. Integrating several mechanisms into one ...

**AC**: not mandating that anyone implement this. Talking about protocol being able to mandate something.

**Christopher Wood**: Let's take it to an issue

**EKR**: two slides bakc, point to network. Addresses are internal to the tunnel. I want packets to come out the other end with UK address, while I have US address. ...

**AC**: edit in the slides that was added later. Not talking about outer address of QUIC/H3 connection. If would give out public UK address, so ... outer packet is outer US addres ...

**EKR**: no NAT needed in this case, internally NATing at the client. many VPNs allow you to select the address range in some way. Does this need to be supported? 

**AC**: I was imaginging that this would happen outside, but can see extension that adds this to signalling mechanism.

**Valery Smyslov**: All these features were discussed in ipsec wg long ago and that ... I suggest you look at our experience. ...  _(very difficult to hear)_


### Individual documents

#### QUIC-Aware Proxying Using CONNECT-UDP (draft-pauly-masque-quic-proxy) - Tommy Pauly (15 minutes)
[Draft](https://tools.ietf.org/html/draft-pauly-masque-quic-proxy-00)

**Eric Kinnear**: Probably will schedule an interim to discuss further, since we only have 5 minutes now

**Tommy**: Interim is good idea. Will take a little bit of time for a meta-comment. 

**Tommy**: Overall, it would be good for us as a wg to define our stance and plan for extensions. Seems clear that we have core items and I agree with notion that we should focus on them and that they should be MVPs. But idea to be able to extend/tweak these mechanisms have come up a lot and these two follow-up presentations are about that. So we should clarify from chairs/AD the plan for how to support extensions, when we do this, and make sure it informs the work that we're doing in CONNECT-UDP for flow-id mechanism etc. 

**Tommy**: if anyone wasn't at interea: confusion that there is some QUIC proxy work brought up that overlaps with MASQUE. Was a sense that MASQUE is focused on some tight use cases and can't be re-used there. However, seemed very similar to IP requirements we have here. Fact that this confusion has happened, means we need to communicate better. Feel that work needs to all be done here. 

**Eric**: Agree. Now is the time to talk about use cases. Folks presenting in intarea should join this discussion here. 

**Mirja**: Agree with extensibility. But too easy to just say we'll leave things to later extension. Need to make sure we have the -right- mechanisms in place, so need to talk about the details a bit more to make sure these mechanisms are correct. 

**MD**: charter isn't super-specific about what bits of UDP and IP proxying need to be in there, and it's premature to decide what is an extension and what not. Let's get consusensus on what we want to address, not how to split into drafts.

#### Additional IP Proxying Requirement Considerations - Mirja Kuehlewind (20 minutes)
 
out of time


### Final comments

**Eric**: Let's focus on use cases we really need to support and gain agreement on that, more so than particular solution we want to deploy. Will also try to schedule an interim to make more progress. In mean time, have conversation on mailing list and issues on github. 

**David**: Thanks all and cheers to the universal deployment of MASQUE _(probably off to listen to some more Taylor Swift now)_

