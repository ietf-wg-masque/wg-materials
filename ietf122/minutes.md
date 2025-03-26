# MASQUE Working Group Agenda - IETF 122

March 21, 2025, 15:00 - 16:30, Friday Session III

## Administrivia (5 minutes)

## Working Group Documents

### [Proxying Listener UDP in HTTP](https://datatracker.ietf.org/doc/draft-ietf-masque-connect-udp-listen/) - Abhijit Singh

Asking for status on implementations. Any others?

(see 2 hands in the room, +1 interested but no time in the chat from Lucas)

Christian: Actually I'm midway through implementing it.

Abhi: That's great, I'll reach out to you before the next IETF.

Christian: That should work.

Eric Kinnear: I think we can potentially run a WGLC on this now and then essentially hold it it until we get enough of the multiple people have actually interopped. Please read it and send comments to the Github.



### [QUIC-Aware Proxying Using HTTP](https://datatracker.ietf.org/doc/draft-ietf-masque-quic-proxy/) - Eric Rosenberg

Marten: Was there any evidence for a server misbehaving? Because this is pretty standard QUIC behavior of the server fails?

Eric Rosenberg: I don't think there's any empirical evidence.

Martin Duke: IF you were using SCONE, you might not want to share your stuff with people.

David: Just briefly to Martin's point, SCONE is completely optional, just don't use it if you choose to go through a proxy.

Matt Joras: What if the proxy wants to pry? Hmmmmm.

David: Just so (didn't catch what he said...)

Martin Duke: Someone asked does this actually matter? Just saying this is useful.

You may not want port sharing because of scone, not saying that is the right direction.

David: Ah, then we're in violent agreement.



Eric Kinnear: Are there other implementors who are able to interop?

David: From Google, we want to implement this but just haven't gotten time. How about we do a trade for this and CONNECT-UDP, and we can interop both at Madrid?

Eric Kinnear: Let's finish up the last remaining issues (no need to do that immediately after this meeting, can start WGLC before Madrid) and we'll head towards WGLC

Lucas: Haven't read the document in a while, there are some obvious questions that come to mind. I think I'll go and file some issues on GitHub. Happy to work with the authors to figure out the answers.

Eric R: That'd be great.

Marcus Ihlar: We have an implemenation of scramble, but haven't tested it with anyone yet, so would be happy to do and maybe the next hackathon is a good opportunity to do that.

Tommy Pauly: Looking at the issue list. We definitely have issues open, some require discussion. Need to close them out or adjust text. I think there aren't any large design gaps left. Please as you're reading, file issues for anything confusing.

Eric K: Are we saying this is a good time to do this, or are you planning to do a editorial pass before that? Sounds like yes, so please read the document and file issues now.

Eric K: We'll do a WGLC before Madrid if we can get the issues closed out by then. Looking forward to hackathon results in Madrid.

### [Proxying Ethernet in HTTP](https://datatracker.ietf.org/doc/draft-ietf-masque-connect-ethernet/) - Alejandro Sedeño

Looking for interop, not much update, is it time for WGLC?

Eric K: Same question, any other implementors ?

Marcus Ihlar: Techinical issues overlapping the hackathon this time, but last time got interop and would like to do something more proper.

Eric K: Seems like a couple of implementations and sounds like we should start WGLC, similar to first one.

Ted: We should get a review from IEEE as well before we do WGLC.

Eric K: Good point, yes sending to IEEE as well.

Craig Taylor: I do intend to raise an issue surrounding MTU, which we spoke about directly, but don't think it will be material with the process.

Gorry: We've been talking about ethernet for a while here, we should get a review from INT area?

### [DNS Configuration for Proxying IP in HTTP](https://datatracker.ietf.org/doc/draft-ietf-masque-connect-ip-dns/) - Yaroslav Rosomakho

David: I'm also planning to implement this, ideally by the next hackathon. When I wrote the first draft of this, the idea was to mirror how addresses are assigned.

Ben Schwartz: I don't see the value in DNS request. I would just eliminate it.

Yaroslav: Do you mean eliminate DNS copletely?

Ben: Yes, eliminate the entire DNS request capsule and just send DNS assign unconditionally. If you want an expression of support, you can negotiate that as a feature negotiation. But I'm not even sure you need that, if the recipient doesn't support it.

Yaroslav: One of the uses is to signal DNS technologies that you support, that's useful information.

Ben: I understand that in principle, but it's not done in any other DNS configuration system that I'm aware of. It's just not necessary.

There's no great value in saving a certain amount of data in the assign response. I'm not even sure it saves data on net, since you have to send more data in the dns request capsule to get less data in the dns assign response. It's already very small. The DNS request capsule is likely to indicate support for a range of things. I would model this on IPSEC or DHCP or IPv6 RA.

David: That's actually incorrect. IKEv2 requests it. I think it's a reasonable question to ask, but there is prior art on this particular approach being taken. I think there is some value in sending less to suport it. If you just assign things that aren't supported, then they'll get dropped, which isn't horrible. If you're making a count of what clients are using what DNS servers.

Ben: You get that from DNS servers, you'll see what's getting used.

David: I think either system will work, to be honest.

Valery Smyslov: In IKEv2 an request is needed because a responder doesn't send anything if it doesn't not ask for it.

Tommy Pauly: I believe further clarification for IKEv2. The request can specify a set of servers that it wants.

Eric K: I saw 1 person say they're interested earlier, but are there others? (2 people tentative interest)

There aren't any other open issues, can we file one to capture the question about DNS request?

Yaroslav: I can file one.

Tommy: I wanted to clarify my shrug. It's not a lack of enthusiasm. I think we would implement after some other use cases of Connect IP. The more elaborate use cases need this, I want to do more interop on those before I can do this.

Yaroslav: For us, the key use case is site to site. When somebody is connecting to somebody else with large sites, then that's where it's actually quite important.

## As Time Permits

### [The MASQUE Proxy](https://datatracker.ietf.org/doc/draft-schinazi-masque-proxy/) - David Schinazi

Antoine Fressancourt: There is a privacy oriented research group. If we can't take the document here, we could do this in pearg.

David: Good idea.

Yaroslav: I think since there's quite a bit of rich history behind MASQUE, there;s a bunch of confusion what does it actually mean. If I speak to another vendor, and vendor says I support masquey, doesn't mean that it will interop. Another dimension here is that there are privacy use cases for MASQUE. There are also enterprise VPN style use cases. Perhaps we should split this into a few lanes. Privacy properties of MASQUE. Second, more technical ones of what does MASQUE actually mean.

David: My focus is the privacy use case.

Eric K: We do have a statement in our charter about non-QUIC transports for MASQUE.

Tommy: One nit, you mentioned how we talk about partitioning IAB documents, so thanks for referencing that.

It's listed as one of the properties. I think it's the parent principle of those things, rather than just another property. Perhaps we can talk about how that's framed.

David: That's a good point, we can split that to its own section.

Tommy: You achieve these properties through partitioning, but the properties that you wanted was hiding something from a website, observer, etc.

David: You're right, that can be under a different header than partitioning.

Tommy: Are we actually going to be able to define what a MASQUE proxy is vs. talk about what it means to use MASQUE. When I speak with people, I have to differentiate between two-hop MASQUE, one-hop MASQUE, OHTTP. Giving the Zoo of different options could be good. To the point about pearg, that could make a lot of sense. At some level, once you're talking about privacy properties, it's a bit more researchy.

Mike Bishop: As you note, this is not explicitly in the charter. The charter does say that you can talk about considerations with respect to various things. If we wanted to squint and slide it through that way, maybe. When we start making claims about the properties, that maybe doesn't belong in this group. I really want this document to exist. Whenever someone comes to the IETF with "how do I make this application and standardize it", this is my poster child. David came here with a vision of a scenario. Over the past 6 years, it's been broken down into discrete components, each of which are useful. It's easier to tell that story if I can point to this as the outcome.

Lucas Pardue: I've said I support this. I think it's a great opportunity. I've worked on MASQUE and I have to explain over and over to people what it means and what we're doing. It continues to expand in various ways. Just this week I was reading something on hacker news, a university student was trying to reverse-engineer Cloudflare's VPN software that had moved from wireguard to MASQUE. They were confused by what it meant. Even then, we weren't doing CONNECT-IP, we do a slightly vendor-specific thing. The principles of the MASQUE proxy do apply, it's just some specific details that are different, and it's all of that nuance that confuses people. Assuming that we're going to finish up MASQUE, it would be nice to have a document that explains what we did and also how you continue the work of MASQUE-like things. What would you do for those? This document could provide some framework for design principles.

Eric K: One of the things we did early on in this group, was write a requirements document, but then we parked it, so there's plenty of precent for kind of looking at what is the shape of the thing that we are building.

David: Ah, I had forgotten about that, yeah we didn't publish that.

Antoine: It's very good to talk about what MASQUE provides in terms of properties, but it's also interesting to see what MASQUE is not yet able to provide. Proxy chaining beyond two proxies is interesting.

Christian Huitema: As Antoine was saying, there's the elephant in the room that if we had to re-do Tor, would we do it with MASQUE? Security issues tend to dig into properties of HTTP/3, QUIC, what is the encryption that we do? In Tor, the client doesn't know who the intermediaries are. Digging into this is something we should probably do.

David: What we deployed with multiple hops was nested. Whereas for something like Tor, kind of more circuits with some point to point.

So that's going to be an interesting case study for sure, but how long it'll take to figure it out is an open one. If there are volunteers, I'm happ to work with you on that for sure.

Ben Schwartz: I think the idea of a history doc is much more difficult. Success has a thousand fathers. There are a lot of people who would want a piece of credit for this. I think that this is better kept out of the RFC series. I think there;s a parallel to WebRTC where the group worked in RTC, WebRTC, W3C took a bunch of some new, some existing technologies, produced a whole feet of RFCs and ultimately these ended up being well known puvlicing under the brand WebRTC, even whein cases where the web was involved. My personal preference would be to say MASQUE is the name of the WG, not the technologies, it is not a corporation, and it is going to be finished, it was a process, and what it did was that it did a number of HTTP exetsions and related technologies taht along with a bunch of other stuff provide a lot of useful capabilities related to proxying.

David: Unforuntalely or forunately, MASQUE became a branch that I really don't want to throw it away. I'm biased. 

Martin Duke: I don't think it matter where this is done, we can do it here, TSVWG, we could AD sponsor, we could do it in the ISE.

Tommy Pauly: Some confusion outside the IETF when we talk about it. In terms of venue, let's do whatever makes sense. Don't love the ISE option. There are opinions, and this is a collboartive effort, and we have venues where we don't need IETF-wide protocol elve consensus that we can still publish something.

David: Agreed, ISE is a backup, but people seem to care.

Tommy: About Tor, it's super interesting, but not sure it belongs here. I could see two documents and that would very researchy and very pearrg. I could also see this be pearrg. Probably a lot of people from here will go to pearrg, so we should discuss with those RG chairs.

David: IRTF has chairs deciding what to adopt, so that may make sense.

Brian Trammell: I concur with everyone that this document should exist. MASQUE kinda answers some of the questions that we posed in panrg. It actually gives endpoint control over some of the ways that things are routed without going all the way down to SRv6. I don't think you should publish this document in panrg, although we would be happy to have you come over and ask us about that. But one outcome would be a reference to see MASQUE in panrg.

David: That's a cool idea.

Eric K: We have some history of doing applicability documents. We can say this is the arrangement of building blocks and what properties you get.

David: On the point of if we want to do it here, I want to punt it until we talk to pearrg chairs but if we decided we should do it over there and they;re like no, I'd rather get a bine check.

Eric K: Yeah I think we'll coordinate amongst the chairs and figure out how do we decide where things go.

Ben: There is no singlular protocol that I can say "I implement MASQUE" and then we can interoperate. There is more work to do, and I think we should do that work.

David: I definitely don't want to boil the ocean. There's cool work in intarea about bootstrapping proxy configs. But yes, MASQUE does exist. If you look at WebRTC, it's also a collection of specs, e.g. JS API, then you need STUN, etc. As much as grand unified theory of MASQUE sounds cool, I don't want to put that here.

Zahed: MASQUE is a number of protocols under an umbrella. I see some value of having MASQUE as defined as like an umbrella. I would like to have this talk about really very focused on the prodcution of the working group if we are doing it here. Mike could also decide to AD-sponsor the document and use this working group to be the place where we discuss it.

David: That's interesting, I see Mike nodding, I'll chat with Mike about that.

Lucas: I have practical conflicts with pearg every single time, we have a room, this is an engineering working group, we have people who have built and deployed these things. Yes we would like privacy input, but that group doesn't seem the right place to articulate the engineering architectures that we have done and will continue to do. I could live with it anywhere, but unfortunately RGs end up taking lower priority than WGs for many folks.

Yaroslav: There is an awful lot of combination of things taht fall into MASQUE, and they all have different properties. You can have a good or bad time depending on how you combine these things. So some recommendations for what to do could be very helpful. Encouraging implementors to make good choices has value.

David: BCP territory gets trickier, but good points.

Harald: You invoked WebRTC, so I am here. WebRTC has an overview document that was written first and published last; it's informational. Really a good idea to think about this.

Poll: This is a topic that I would be interesting in spending time discussing.

Results: 37 yes, 2 no, 3 no opinion

Eric K: Thank you for all the energy to making this all happen and we've built a whole bunch of stuff together, we'll take an action item to figure out where this should go.

