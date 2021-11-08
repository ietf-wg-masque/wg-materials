# MASQUE Working Group Agenda - IETF 112


## Time and Date

* Monday, November 8, 2021, 12:00 - 14:00 (UTC), Monday Session I
* [IETF Agenda Link](https://datatracker.ietf.org/meeting/112/agenda/?show=masque)

## Links

* [Meetecho remote participation](https://meetings.conf.meetecho.com/ietf112/?group=masque&short=&item=1)
* [Meeting chat](xmpp:masque@jabber.ietf.org?join) 
* [Notes](https://codimd.ietf.org/notes-ietf-112-masque) 
* [Minutes](https://datatracker.ietf.org/doc/minutes-112-masque/)

## Administrivia (5 minutes)

* Blue sheets / scribe selection / [NOTE WELL](https://www.ietf.org/about/note-well.html) 
* Agenda revision
* [slides](https://github.com/ietf-wg-masque/wg-materials/tree/main/ietf112)

Notetaker: Robin Marx
Jabber Scribe: Lucas Pardue

## CONNECT-UDP and HTTP/3 Datagrams (60 minutes)

### [HTTP/3 Datagrams](https://datatracker.ietf.org/doc/draft-ietf-masque-h3-datagram/)


**David Schinazi**: Lot's of redesigns have happened lately: lots of churn in the drafts. 

**David**: Will now provide an overview of everything still to be discussed

**David**: Overview of things that use HTTP Datagrams atm (use cases by means of extensions). Various different proposals (Connect-IP, ECN markings, ICMP, PMTUD for HTTP Datagrams, Prioritization in WebTransport). Can all be viewed as extensions to Datagram concept. 
**David**: We're sending payloads, but there's interest in sending multiple types of payloads at the same time, so there's need for a demultiplexing approach (e.g., compressed vs non-compressed). SImplest way to do this is send an ID at start of packet
**David**: Main question on the list: why deal with this now? These are extensions, deal with them now? Answer: don't want to build ourselves into a corner. Valuable to have this discussion up-front to make sure we're extensible enough in the core protocol. 

**David**: So what will we want for these extensions? 

    1. demultiplexing 
    2. deal with intermediaries not updating as often as endpoints 
    3. extensions that apply to multiple protocols (e.g., PMTUD discovery)
    4. make mechanism optional (reduce complexity and overhead)
    5. zero latency extensibility (e.g., negotiation in first flight)
    
**David**: Further on point 5. : if something is optional, not everyone will implement it. One of design properties of HTTP: if client sends request, it doesn't know full feature set of proxy it's talking to. Can't send full thing, because don't know version you're talking to. Really don't want to wait full round trip between client and proxy to negotiate support for a given extension. 
**David**: example option: send connect-UPD together with QUIC initial in the same packet. If proxy accepts: all well. If no support: just drop the initial. Saves an RTT between client and proxy. Send optimistically. 

**Ian Swett**: My understanding: this diagram just requires core functionality of the draft, not any of the optional functions/demultiplexing? 
**David**: Yes. Later diagram has extensions in it

**Tommy Pauly**: Respond to Ian: as we look into different options how to communicatie: has impact on HOW you send early data (i.e., what capsule types are allowed). Should be possible with all setups, but sometimes limits to what you can send in first flight. 

**David**: Just putting ID at start of HTTP datagrams is a simple solution. However, if optional: it's unclear if there is an identifier at the start or not... so how do you know? 

**David**: Example when you want to do extensibility without latency cost. Connect-UDP to target and want to use ECN, but don't know if the proxy supports that. So you indicate in some way (e.g., header) that you want the ECN extension. Now, the proxy does need to know if the datagram has a demultiplexing identifier at the front or not. 

**Vinny**: So proxy doesn't know about the extension? it allows it to work end-to-end?

**David**: Let me clarify what end-to-end means here. In this case, extension is between client and PROXY, not client and SERVER. E.g., in ECN scenario, extension would allow proxy, when reading UDP packets that have ECN markings at IP layer, to forward ECN markings back to the client. If there is ANOTHER intermediary between client and proxy, that DOESN'T need to know about extension. 

**Ted Hardie**: What about e.g., robust header compression for IP headers. 2 different ways: I'm compressing towards proxy OR want proxy to go end-to-end to target. In this second case: you wouldn't negotiate extension to proxy. Proxy would just treat connect-IP payload as if it was a normal packet and server needs to deal with it. 

**David**: That's correct Ted. Extensions transparent to the proxy (client to target) are out of scope. HEre, we're only talking about extensions between client and proxy. Client to server: proxy doesn't/shouldn't/can't care, because encrypted in QUIC.

**Ted**: might come some cases that proxy needs to know that what's going on isn't malformed (esp. at IP layer). But probably not the main topic, so let's move on.

**Ian**: Proxy here is what ekr referred to as forward proxy, and target is one or more reverse proxies/end machines, etc. We don't care what it is exactly. Is that accurate?
**David**: Correct. Let's say using Connect-UDP as IP blinding service. From service client is just IP and port to send/recv. Worlds of complexity on either side, but we don't really care. From client's perspective, there could still be an intermediary and proxy, but it shouldn't care. Should just connect to proxy with given authority. What we need to build here is that if an entity wants to deploy the UDP proxy behind intermediary, that should work. That means we can't simply depend on SETTINGS for everything.
**Ian**: Why not?
**David**: In a given HTTP deployment, intermediaries are pretty common. E.g., Google frontend (intermediary) and backends (here: proxy). SETTINGS are hop-by-hop at HTTP, but requests are end-to-end. In particular useful for WebTransport.
**Ian**: In this case, Google target is at least 2 or 3 layers of load balancers in front of application server. So first LB needs to communicate correct settins to things behind it. Is that it?
**David**: WebTransport example is good one: One server that's backend, but that talks to many different servers internally. But client doesn't need to care about that. If some of the fleet supports WT with extensions, some without. THe frontend doesn't know all the supported features of all the different internal servers. Need to send a request+response to know fully what they support.
**Ian**: So the server/LB, needs to know whether connect-ip/udp/wt is supported, becaus ethe way you load-balance these protocols are different from normal HTTP, so you still need that knowledge at the LB
**David**: The intermediary does need to know how to proxy HTTP datagrams (esp. for WT), BUT if we want to e.g., add extension the goal is that the frontend that just speaks WT, doesn't have to be updated every time we want to deploy a WT extension. 
**Ian**: That makes sense. Just wanted to make sure LB needs to indeed understand what type of traffic is being routed. 

**David**: Current design for zero-latency: Datagram Format Types. Client sends REGISTER_DATAGRAM capsule "I'll be sending UDP payloads on here". Sec-Use-Datagram-Contexts HTTP header used to indicate extensions. If used, EVERY datagram starts with a varint. Negotiated using REGISTER_DATAGRAM_CONTEXT. 
**David**: This setup is a bit complex though, discuss further in issue #84.

**David**: We also came up with an alternative. Removes Datagram Format Type, as well as registration+close capsules. Just use capsule types as the main extension point. Clients and proxies will drop any capsules it doesn't understand, so extension can just register/use a new capsule and use that.
**David**: This way, you send a header as part of your request. If the proxy replies with that header as well, you know both have support for these extensions, we want demultiplexing, so now every QUIC Datagram starts with the HTTP Context varint ID. (notetaker: I'm utterly lost here...). 

**Ian**: We're using word demultiplexing. Does that mean taking a single stream or sending to different locations, or is it mainly format conversion? 
**David**: Demultiplexing: separating things in an incoming flow of things. So not to demultiplex to different locations. Rather: this is coming in as compressed packet, need to decompress and then put in stack. If uncompressed, put it in stack directly. So demultiplexing within the proxy. i.e., demultiplexing to software that knows that to do with it. 

**David**: This setup supports all the earlier requirements (multiplexing, oblivious intermediaries, simple cross-protocol extensions, zero-latency). 
**David**: Thought excercise: what if we extract all these things into a sepearate draft, then what do we still need in the base drafts? Answer: just the capsules that allow you to send stuff before you know what the server supports. 2 capsules needed: 1) Datagram with or without context 2) Datagram with context. Might just call it UNEXTENDED_DATAGRAM and simple implementers can just use same implementation for both options. More advanced setups can then tweak semantics for datagrams with context. 

**David**: Open questions: any new requirements? problems with new proposal? other opinions?

---------------

**Vinny**: Ideally less complex is better. By default, all proxy auth methods will work with MASQUE?
**David**: Yes. All are traditional HTTP req/resp. You can send any HTTP header, so pre-xisting auth heades are indeed supported

**Martin Thomson**: _redefining dark mode by himself_ a lot is bound up with design of protocols that will be using H3 datagram stuff. I've been convinced back and forth of the values of some of these things. Ultimately what we want here: send bidir stream to other end + associate bunch of datagrams with it. Ability to fold stuff in, associate stuff, etc. that's something those higher protocols can deal with. If intermediary supports upper protocol: fine. If not: no business participating in that. Simpler design here and all-over if don't do this. So: no capsules at all would be my preference.
**David**: We have a Datagram capsule. Goal is to send DGRams over non-H3 HTTP versions. Common deployment model is to have H3 from client to first intermediary. ANd then use other version (E.g., H2) from the intermediary to proxy. Intermediary has to convert to dgram capsule to send to proxy. Idea in the current draft is that you the DGRAM capsule and intermediary speaks that, but that's it. Intermediary doesn't need to do more. Is that what you suggest?
**MT**: I suggest you re-invent them. (notetaker: didn't quite follow this)
**David**: Real question is: do we want to have common way acros protocols, or have each protocol do their own. Feels silly to have everyone re-invent same wheel. Folks seem to like capsules = series of TLVs, but see we don't agree

**Ben Schwartz**: Getting a little confusing; talking about multiple independent layers. People might not have caught that DGRAM format types are unrelated to the capsule discussion (run end-to-end). Two types of intermediaries we're discussing: 1) smart 2) dumb. 1) can do HTTP version conversion magic with interweaving/deinterweaving into a TCP stream for example 2) just forward directly. Case that gets you in trouble are dumb intermediaries that talk to H2 backend. If I'm an intermediary that does do H3, but forward to H2, don't know that supports things. Can't interpret, also can't just forward, can't do anything. Note: imo we're re-developing things already in WebTransport, so alternative would be to run this over WT

**EKR**: You're configured with URL and settings of endpoint you're connecting to. If endpoint doesn't support settings... that just shouldn't happen. Client should be pre-configured and know what the other side supports up-front. Intermediaries shouldn't be an issue here. They should only translate things that make sense to it and make sense to things behind it, nothing more. 

**David**: Folks want to deploy this behind frontends. You're saying: we terminate everything at that first hop, so there is no more intermediaries... I don't think that simplifies things (notetaker: missed something here). For example, focus on WT: folks want to deploy this on backends through frontend. 2 ways of doin that: 1) request is end-to-end client to backend with intermediary (current design) 2) frontend terminates the request. Frontend then creates different, new request from frontend to backend. That would remove intermediaries/sharing of state/etc. HOWEVER if you want to add any extension to WT, then the terminating intermediary needs to support it, instead of just modifying the client and server

**EKR**: Terminating is just one option. Other is to have an agreement between proxy and server on what is used in what way. Now, you're putting to onus on the client. It makes more sense to have it between intermediary and backend, not client and intermediary. 

**Victor Vasiliev**: The way this models extensiblity for Connect-UDP is that CUDP is a resource and anything you negotiate is property of individual resource instead of property of the connection. I agree with EKR (...) still need standardized interface between intermediary and server. WOrks for both UDP proxies and masque
**David**: So have http datagrams function so that we don't need to determine things at the intermediary. 
**Victor**: Yes. SImilar to things like HTTP headers

**Chris Wood**: No more time. Many different hot takes on moving forward. Let's make a design team to resolve these issues, followed by intermediary between now and IETF 113 to build consensus. If you want to participate on design team send me or Eric Kinnear email or move in the list. David will lead the design team. 

**Lucas**: If we get rid of capsules, we're back to where we started with normal HTTP/3 Datagrams. Whole reason for adding capsules is doing datagrams over H2 and H1. We would have to revisit that decision then. 



### [CONNECT-UDP](https://datatracker.ietf.org/doc/draft-ietf-masque-connect-udp/)

**David**: Was on hold a bit while discussing HTTP Datagrams. Redesigned a bit since: No longer Connect-UDP, but relies on Extended CONNECT. Use pseudo-header :protocol = connect-udp.

**David**: Another important change on the client. For example via client. Before just gave host+port, now we use a concrete URL template (decided at config time). Path contains the target host/post (authority remains the same as traditional HTTP: :authority is the proxy, target host is encoded in the path).

**David**: Thought this would be simple to define a URI template, but in practice it's very complicated + security issues. Do we really need a URI -template-? Can't we send in separate headers instead? Use the URI template mainly for configuration parameters. 

**MT**: Why would you move resource identity to a header? Queston: what resources are you identifying here? Generic resource that can connect to anything, such that having IP addresses in url is a mistake, of is it a resource that has generic connection capabilities?
**David**: This is the question we're trying to solve. Is the resource the part of the proxy that handles ConnectUDP OR is the resource the target? If the target: want target encoded in the path. If resource is proxy, then don't want in the path.
**MT**: Different access control depending on path you're going to? THat would imply URI? If you use headers to identify resources, they're no longer headers anymore.
**David**: Agree. But resoruce isn't target, it is the proxying capability.

**Vinny**: Can you elaborate on how you envision header parsing to be simpler than URI parsing? Same type of parser in both?
**David**: ATM, on the client, I need to parse the URI template. You have to run some code on client that'll take configuration and target. e.g., config is URI(template) and browser will say want to connect to target X. You combine both and compile them into HTTP header stream. Right now, need to parse URI template and expand into fully resolved URI. Need to understand which parts are what exactly. HOWEVER if we use config URI: would just use regular URI, put stuff in headers, and add normal parameters to the URI. Also makes server side easier, just need to look at the headers, no need to split from URI.

**EKR**: No strong opinion on this. Additional security isn't that big of an issue. Conceptually using headers is wrong. Verb is CONNECT, resource is what you're connecting to, not the proxy (which is how regular CONNECT works). 
**David**: Mainly care because it's a pain to implement. COnceptual aspects of HTTP aren't my main concern.

**Ben Schwartz**: URI templates mandate full unicode support. You can have very weird interactions here (e.g., RTL reversal unicode). Anything is possible, gets complex quite fast. Middleground: everything needs to go in URI and we specify the URI rather than fully templating it.

**Tommy Pauly**: Implementations that support DoH already support URI templates. People doing MASQUE probably already do DoH. @Ben: probably indeed need to have some limits on what you can put in the template URI. For Connect-IP, it's a stronger argument for why these properties are part of the resource (as opposed to for CUDP). I think we should leave it in the URI and just add constraints on how funky the template can get.


**David**: Using :protocol = connect-udp, what do we use for HTTP/1.1? Currently, we use Upgrade mechanism, same as WebSockets. Question: what METHOD do you use? WebSocket uses GET with Upgrade, current draft uses CONNECT with Upgrade. Do people have opinions on what's best?
**crickets**: Are silent
**David**: Ok, let's keep it like this

**Martin Duke**: IIUC, if we ran into server that didn't understand Upgrade, with CONNECT we'd end up sending stuff into a random TCP connection from the proxy, whereas with GET we'd return a 404, right? 
**David**: Yeah, that'd probably be it, as GET with body isn't technically allowed. Probably not a 404, but yeah
**MD**: That seems better than semi-undefined behaviour with CONNECT. 
**David**: In H1 you send authority without path. Here, you'd send connect with / in there. I'd expect it would fail spectacularly at DNS resolution, rather than actually acting on the CONNECT, probably
**MD**: Whatever has the most deterministic/less goofy failure path is better. 
**David**: Agreed. Maybe we need to test some existing servers and see how things fail

**Erik Ngyren**: If we're using extended connect for many things, should pick something we can keep using. E.g., new method like CONNECT-X is maybe better.
**David**: Would prefer not a new method, but indeed, let's stay consistent.

**MT**: 1 option is to ask httpbis wg what the answer is. This is something they're currently examining for H3 integration with Extended Connect. Maybe this is a generic HTTP problem and not something that MASQUE needs to solve.
**David**: Good point, will take to the HTTP wg to see what they think.

**David**: _is ambiguous about whether he'll stay or go take a nap now_

## CONNECT-IP (20 minutes)
### [IP Proxying Support for HTTP](TBD)

**Tommy Pauly**: Often heard: why is this different? also: have had many different proposals. Now: single, merge proposal! yay

**Tommy**: Motivation: allow generic IP proxy through HTTP proxies, not just TCP/UDP proxying. Typically useful for VPNs. Can also think of it as generic connect-like proxy for arbitrary IP protocols (similar to Connect-UDP in that way). 

**Tommy**: New scope: Extended CONNECT protocol, similar to CONNECT-UDP's approach (keep them aligned as much as possible over time). 
**Tommy**: Proxy always deals with entire IP packets that are contained in the content of HTTP datagrams
**Tommy**: Concerned only with things that exist in the IP header itself. Endpoints can only use those fields. SRC/DST address + next protocol
**Tommy**: Out of scope: header compression (should be extension), ICMP signalling (should be common for UDP and IP CONNECT), integration with protocol-specifics (also extension)

**Tommy**: What is defined atm: 1) connect-ip as value for :protocol 2) URI template to indicate target and ipproto (here it doesn't make sense to talk about host and port like with UDP) 3) some capsules ADDRESS_ASIGN (you can send from this address), ADDRESS_REQUEST (I would lik to send from this address/subnet), ROUTE_ADVERTISEMENT (defines destination that side is to send to) 4) IP_PACKET HTTP Datagram Format (see previously, probably will change). Basically says: this DGRAM contains a full IP packet, other extensions can do something else.

**Tommy**: Simplest of VPN use cases: client can send to everything in the world, but some use case are more restricted (E.g., split-tunnel or only able to reach subset on the other end). Supported by current approach.
**Tommy**: Also allows proxies to share multiple IP addresses between multiple clients
**Tommy**: Provides some concrete examples (see slides)

**EKR**: Good starting point

**David**: Really wanted to thank Tommy for the work spent on aggregating the two CONNECT-IP proposals. Would love to see this adopted now. 

**Eric Kinnear**: Use show of hands tool to hum. Question "Is this a suitable starting point for WG adoption"? Result: 46 raise, 2 do not raise (96 total participants present in chat)

## New Work (remaining)

### [HTTP Datagram PING](https://datatracker.ietf.org/doc/draft-schwartz-masque-h3-datagram-ping/)

**Ben Schwartz**: Individual, non-adopted draft, wrt PMTUD 
**Ben**: Very much ented on the Datagram Format Type (if that setup changes, this also needs to change of course)
**Ben**: PINGs are opaque datagrams to intermediaries. Each contains even number and padding, response contains next number and no padding
**Ben**: Enables DPLPMTUD for CONNECT-IP (needs some minimum MTU). However, a very general feature that's also useful for anything using HTTP Datagrams. Can also be used for latency/loss rate measurements. 

**Ben**: One open question: can/should we make PING (end-to-end) reliable in this draft or re-use something existing? 

**Magnus Westerlund**: Interseting mechanism, but doesn't solve actual end-to-end problems. Before I can tell if actual flow is supported or not, because first need to probe if MTU is supported, then can decide. So has downsides still.
**Ben**: Learning MTU is always async, never know before you start. Questions is mainly: how long do you have to wait. 
**Magnus**: Cne crux for tunneled protocol: you know what the MTU will be for first hop (via QUIC), but you don't know the later hop MTUs. One things that should be considered in MASQUE design team should be MTU aspects.

**Ben**: If we used SETTINGS frames to communicate MTU for datagrams, then that implies knowledge of all the paths to all of the backends of that proxy. It's not just about compat with other proxies, but also all the network elements that stand between them.

**Martin Duke**: Useful exercise. Wondering if you have a protocol running over MASQUE, typically these off-the-shelf protocols will have their own PMTUD mechanism, typically end-to-end. So why do we have this in MASQUE itself?
**Ben**: Fundamental problem: in order to be compliant IP implementation: need at least 1280 MTU. Transport protocols are encourages to use PMTUD, but also allowed to cap sizes at 1280 (IP specs say you don't need MTUD then). If MASQUE wants to be compliant, need to always provide functional delivery of 1280-sized packets. To do that, we need this as MASQUE level as well. 

### [HTTP Datagram Prioritization](https://datatracker.ietf.org/doc/draft-pardue-masque-dgram-priority/)

**Lucas Pardue**: _speed run through the slides_

**Lucas**: See if there is interest already, or maybe pick it up later. 
**Lucas**: Lot's of background on priorities in HTTP. For now, nothing defined for prioritizing datagrams. 

**Lucas**: _mints today's coolest slide title (the sinister 6 P's)_
**Lucas**: Proposal based on HTTP extensible priority scheme (in HTTP wg post-WGLC at this time)

**Lucas**: Main concept: datagrams might have different priority than the streams they belong to
**Lucas**: Main question: who cares about this? Is this useful to define? 


**Tommy**: Clarifying question: explicitly different priority than the stream. Why? What is the rationale? e.g., for Connect-UDP, stream will mostly be DGRAMS anyway?
**Lucas**: Stream priority would be default yes, this just allows a bit more fine-grainedness.
