# MASQUE at IETF 120

# Quic Aware Proxying
Speakers: Tommmy and Eric

Nice logo of QUIC covered with a MASQUE

Changes:
* Design team input
* Scrmbling forward mode 
* Security considerations
* Loop prevention
* Editorial cleanup

Proxy is now responsible for ID selection

Terminology and examples before details

### Open Issues

#### Issue #113
Prefered address migration: Target says "migrate here", how does proxy handle it?

Options: reconnect, signal proxy new address, something else

Ben Schwartz: Clients can do what they want

Mike Bishop: Proxy doesn't care. Separate request works.

Tommy: If client used name, client might not know address, might do extra work

Mike Bishop: Why would the target do that?

Alessandro: Capsule adds complexity

Mirja: I agree with Ben: why do we need to say anything

Christian: CONNECT-UDP is right solution, you may want to use a different proxy

Magnus: Just connect again

Alex Chernyakhovsky: Simple, but should have text for name initial connection.

Mirja: Why here, not somewhere else

David (off mic): The other one's already published

#### CID registration limitation
Eric: should proxies advertise limit on CIDs or just cancel registrations above limit?

Tommy: how to communicate
Eric: offset base, similar to flow control

Antoine Fressancourt: sound thing to do, credit based mechanism

Mirja Kuehlwind: We should do something, why not same as QUIC? Copy whole CID mechanism. Seems big, but it works.

Ben Schwartz: Was going to say same thing. Could just use same limits from transport params.

conversation ensues about coupling making this a good idea or not

Kazuho Okhu: +1 Mirja, would want flow control to avoid rapid reset issue.

Marten Seemann: +1

Eric to write flow control text

#### Client VCID Length
Should be as long as the one in the client connection. Should SHOULD be MUST?

Magnus: What if migration inward happens? Why does proxy see?

Tommy: about registration. Just needs to be long or longer than in capsule

# MASQUE CONNECT-UDP Bind
Client listens at proxy for incoming packets

Add header, target has \*

What's new? Compress the targets

Two new capsules, one for grabbing compression, one for releasing it. Compression removes IP information.

Context ID instead of repeating target

Alex Chernyakhovsky: Are these IDs reuseable?

David Schinazi: hope answer is no. Decided in an RFC 9297

Alex: text should be clear we release resources not number

When proxy can't do more, can respond with compression close to reject. Clients can do same

Not only compression: also allows specification of which sources are needed

Proxy advertises public address to clients

Discussion: more than one per family? ICE dealt with this. Strong opinions let us know

Right now proxy picks address for a target. If benefit to client picking let us know.

Ben Schwartz: can extend later if needed.

Jonathan Lennox: seeing these two in a row, not sure the work together. Third extension for running QUIC server through MASQUE?

David Schinazi: before we move, last open issues. Editorial pass, and then how do chairs feel about WGLC?

Tommy: Implementation and testing status. Would be nice

Implementations of previous versions, new additions relatively completed. Finish up and editorial pass. Once interop between implementations and anyone wants to join last call.

Who read? Who wants to interop/hackathon? show of hands

Zaheduzzaman Sarker: don't feel 6 people means 6 implementations.

Chairs volunteer to help coordinate with interop.

# Ethernet proxying
Presented by Alejandro Sedeno

Open Issues - Vlan tagging, client, ethernet versions, layer separation

VLAN - Recommend each VLAN different connection though you could tag in here if things work

Didn't say how to differentiate. Borrowed a lot from CONNECT_IP, stripped out template language, should put back and let you name networks. 

Naming networks not numbering VLANS

Thumbs up in room

Watson: names run to numbers?
A: dropped onto interface on a VLAN, tags are appropriately put on each side. 

Supported versions:
Just say 802.3 Ethernet v2 frames

Magnus: Think this is fine. Only issue is multiple versions. Hopefully compatibility is here

Dennis (chair hat): Think we have a commitment to ask IEEE about this. Can sort out final stage, or sooner

Alejandro: Sooner!

Mirja Kuehlewind: think WGLC is right time to not ask each time we edit

Layer separation and congesion control
Do we peek or not? Protocol not decide, but extension if people want it in an appendix

(unkown): helpful pointers, or "good luck"

Alejandro: might say more than TCP MSS clamp but not "fiddle so"

Tianji Jiang: Ethernet frame begining to SCS. If SCS is stomped on does that affect it

Alejandro: issue about carrying it earlier. Rejenerate if needed

Tianji: 5G says must strip, if using here, have to regenerate

Juliusz Chroboczek: Confused. Protocol is used to connect onlink talking devices, and now VLANS?? Need a little expansion

Chairs: will close out, then ask similar questions. If implementing let us know.

# DNS Configuration for MASQUE
Speaker: David Schinazi

Not DNS over MASQUE
Rather about which resolver to use

/etc/resolv.conf

VPN protocols do this. IKE, OpenVPN, etc.

CONNECT-IP sends capsules for data, ROUTE-ADVERTISEMENT says what routes to serve.

DNS config has to be out of band. Makes life hard.

DNS_ASSIGN/DNS_REQUEST - IP address, internal domains, DNS search domains, request IDs

Inspired by IKEv2

so can say corp.example.com goes through one resolver

Also configure DNS protocol to use
We have ways to do this with SVCB

Ben Schwartz: Worth solving. Step in right direction. Really think wrong layer. Right layer is interesting question. What is bootstrap point for a MASQUE deployment? What is sent to device? Presumption is CONNECT-IP, CONNECT-UDP template, but we might need addtional info and think carefully.

We actually want multiple protocols: QUIC vs. CONNECT-IP/CONNECT-UDP, quic optimized ones, but need them all. Also TCP tunnels, so want CONNECT-TCP. Bootstrap thow in DOH, or maybe provisioning domain.

David: yes, and. Deployment model is swiss army knife, which Apple did. For that usecase works. But not only usecase: Chrome and ChromeOS where stack can't silently upgrade things aware ways. Chrome configured to do the cool thing, but ChromeOS needs IP level connectivity.

Long discussion of how this works or not and bootstrap.

David: agree with vision, but doesn't quite work.

Ben: Can still do that with blob, ignore rest.

Watson: To reach DNS can do it over tunnel, just advertise address as one served

David: yes

Tommy: Different from bootstrap case. Bootstrap in INT area somewhere. Comes down to sending separate request to proxy of "tell me your config". That's not this. Useful to explain how narrow: not all CONNECT-IP, just some uses, VPN usecase where trying to act like a tunnel.

David: yes, got it

Tommy: if you get down road of the other things, move to format that supports encrypted DNS. If name supports H3/H2, same name as server could just send DoH there. Optimization.

David: already int he draft

Alex: Why not both? not incompatible. Orthogonal or complementary. Avoids staleness, etc.

Servers over tunnel? Should say not issue if DoH, DoQ, etc. but bad if Do53 outside tunnel. 

David: will add, makes sense
David: one aspect of connect IP

How do people feel about adoption etc?

Josh Cohen: like info from DHCP?
David: yup

Worth grab meal with opinions and coalesc, or make clear how narrow this is.

Lars: take look if charter allows. Says nothing about DNS.

# What's next for MASQUE

Issue lists getting shorter and shorter. Making progress. Many people reading and participating. Good point to read throuhg and provide feedback. All current ones to wrap up.

What's next? Who has deployed? Could be to one Pi or a bajilion customers?

How is that going? Thumbs up

Drafts with solutions, but not necessary

Zaheduzza Sarker: I would also like to know, especially with extensions, and other things built. Necessary to understand how it has gone.

Lucas Pardue: multiple deployments of different kinds. Tooling sucks. In scope? IP tunneling over QUIC to nested congestion control. Curious if in scope.

Chairs: tooling in scope for discussion, can talk about charter/combine with quic, will find home

Tried to steer clear from it, ICCRG can talk about TSVWG talked about it, when you learn more about sharing can share it somewhere. we'll figure it out

Alex: QBONE is still deployed, original QBONE said we want nested CC but not nested reliable with early rejection from congestion control with BBR. CUBIC over BBR is good, BBR on CUBIC great, CUBIC on CUBIC sucks. Horrific hacks to fix this

Area could use research and tooling. 
