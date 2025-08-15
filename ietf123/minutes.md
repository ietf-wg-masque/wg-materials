# MASQUE Working Group Agenda - IETF 123

### Time and Date

* July 22, 2025, 11:30 - 13:00, Tuesday Session II ([See this time in your local timezone](https://www.timeanddate.com/worldclock/fixedtime.html?msg=MASQUE+%40+IETF+123&iso=20250722T1130&p1=141&ah=1&am=30))
* Location: [Tapices](https://datatracker.ietf.org/meeting/123/floor-plan?room=tapices)
* [IETF Agenda Link](https://datatracker.ietf.org/meeting/123/agenda/?show=masque)

### Links

* [Onsite Tool](https://meetings.conf.meetecho.com/onsite123/?group=masque&short=masque&item=1)
* [Meetecho Room](https://meetings.conf.meetecho.com/ietf123/?group=masque&short=masque&item=1)
* [Meeting chat](https://zulip.ietf.org/#narrow/stream/masque)
* [Notes](https://notes.ietf.org/notes-ietf-123-masque)
* [Minutes](https://datatracker.ietf.org/doc/minutes-123-masque/)

## Administrivia (5 minutes)

* Blue sheets / scribe selection / [NOTE WELL](https://www.ietf.org/about/note-well.html)
* [IPR Disclosure related to draft-ietf-masque-quic-proxy](https://datatracker.ietf.org/ipr/6744/)
* Agenda revision

## Updates since last meeting

- Marten: WGLC finished for Listener UDP and Ethernet

## IPR Disclosure related to draft-ietf-masque-quic-proxy

- Dennis: There has been a disclosure that you should go read online

# Working Group Documents

## [QUIC-Aware Proxying Using HTTP](https://datatracker.ietf.org/doc/draft-ietf-masque-quic-proxy/) - _Eric Rosenberg_, 15 minutes

### [Issue 116](https://github.com/ietf-wg-masque/draft-ietf-masque-quic-proxy/issues/116): Use of the term ECB

- Martin Thomson: This isn't something we need to concern ourselves with especially. We need to make sure that what we're doing is honest and has been reviewed as appropriate, but this is in the details of the implementation of a crypto system. If people don't understand that ECB when used in this way has a set of properties, then that's okay. You could use CTR in this context, as well, and it's exactly the same thing, but it wouldn't be as honest.

- David Schinazi: Agree with MT. Add a reference that says "this mirrors the construction in RFC9001" and people take their questions there.

- Mike Bishop: I think this is fine from a technical perspective, but I wouldn't be surprised if during IESG review there is a question, so have an answer ready.

- Tommy Pauly: The text does currently reference that section in RFC 9001, so I think the proposed resolution is "Close this issue".

### [Issue 85](https://github.com/ietf-wg-masque/draft-ietf-masque-quic-proxy/issues/85): ECN Forwarding

- Martin Duke: ECN thing is fine. Network can modify this bit, so it's a crummy channel to use to exfiltrate info since anyone on the path can mess with it

### Hackathon, Interop

- 3 implementations that all interoperate, including with Scramble

- Tommy Pauly: We've learned some things from the hackathon where the document was accurate, but we've made some editorial improvements. Doing this really helped make the text more clear, including for those of us who wrote the text in the first place. We're going to try to cut a

- Plan: Cut a new version of the document this week or next and it should be good to go for WGLC.

## [Proxying Listener UDP in HTTP](https://datatracker.ietf.org/doc/draft-ietf-masque-connect-udp-listen/) - _Abhijit Singh_, 15 minutes

- Abhi: Interop update. Able to interop with Marius, thank you! Got some good feedback from the WGLC.

### [Issue 40](https://github.com/ietf-wg-masque/draft-ietf-masque-connect-udp-listen/issues/40): echoing COMPRESSION_ASSIGN is wasteful and error-prone

- David Schinazi: This is a cosmetic change, if we'd done it ages ago sure, but at this point I'd rather not make changes that we don't have to.

- Marten Seemann: What if I receive an echo with the right context ID, but a different IP address?

- Abhi: I do drop capsules if they're not valid, though

- Marius Kleidl: You have to check if it is an ack or an assignment from the other peer. If you can have an explicit capsule type, that would be great, but it's not a hill I want to die on.

- Tommy: I don't have a strong opinion here, but just looking at other capsule designs like what we have in quic-aware negotiating CIDs. We do have an explicit ACK capsule, but it can also add to the information that it's ACKing. It would be okay to include the information, yes you have to validate it, but it means that when you reflect it back, it confirms that you understand the Context ID means these things. Yes it's a little verbose, but it's very clear what you're acknowledging. I think it's okay to leave as-is.

- Abhi: Not seeing particularly strong opinions

- Martin Thomson: I'm with Marten on this one, we've done this in a number of places where we have redundant information that needs to be checked. It's annoying and wasteful. I know folks have implemented this. We have a clear way of managing transitions between different versions, and I think those people understood the risk they were taking when they implemented and deployed things. I'd fall on the side of saying that we should start with the minimal thing and reduce the risk of silly errors. I hear Tommy's argument, but that's most useful when there's extra to add. In this case, it's "I'd like to set up this context" followed by "OK". We shouldn't communicate the information unless there's a good reason to do it, and I think in this case there is none.

- David: The Mart[ei]n's have convinced. WGLC is pretty much over. What we'd decided to do after was to renumber the capsules. One last compatibility breaking change, since we have to make a change anyway, we can live with this.

- Abhi: I'm okay with that. Do we need that for closing? 

- David: Do we echo the close capsules? 

- Abhi: No, we don't.

- David: You stop using the context the second you send/receive closing, so you don't need to know that the other side heard it.

- Marius: Thinking a bit more about results from interop, there are more things: 

    1. Proxy public address header is a sf-list, but it's not clear what the items are. We ran into this problem yesterday, one of us thought strings, the other tokens.

        - David: It can't be tokens, since they're IP addresses and have things like `:` in them. File an issue and we'll write a PR.

    2. Proxy was binding to an IPv6 address and then the client was sending datagrams with IPv4. That doesn't work, so it would be great to add a recommendation that the MASQUE client should check the address that the proxy bound to in order to see if it can use that with datagrams, otherwise those packets are lost in the void.

    3. For the MTU thing, we also ran into it. The MASQUE client is likely aware that an uncompressed context has a slightly lower MTU, but the other peer that is sending data to the MASQUE proxy may not know. The proxy should recognize datagrams that might be too big and might be dropped and just start a compression context automatically to raise the probability that the datagrams get through.

        - Abhi: I think these are very good findings from the implementation

- Lucas Pardue: On the QUIC-aware forwarding call, there was an issue about structured fields for lists and other things. Perhaps look at the text that Tommy landed on, and I'm happy to review the PR to ensure consistency.

- Jnathan Lennox: Given that the use case for CONNECT-UDP Bind is WebRTC, has anyone thought about how you specify that? It's https, but doesn't say to use MASQUE or anything?

- David: From the perspective of the rest of the network, MASQUE server is the same as a NAT. You just need to know the address and port, not that MASQUE is involved.

- Jonathan: Browser is connecting to the MASQUE server to run a Bind.

- David: That's part of browser proxy settings, not ICE.

- Jonathan: Something still needs to know what to bind, but I guess that makes sense. 

- David: You use this draft when your proxy settings tell you to use CONNECT-UDP, and you're about to bind a socket.

- David: In response to Lucas's point, can you ask for an early review from the HTTP directorate, since having an extra pair of eyes would be helpful.

- Benjamin Schwartz: I think that the best way to model this is to treat the proxy as a virtual interface, which means that the ICE candidates you create are host candidates.


## [Proxying Ethernet in HTTP](https://datatracker.ietf.org/doc/draft-ietf-masque-connect-ethernet/) - _Alejandro Sedeño_, 15 minutes

- Dennis Jackson: Is anyone working on interop at the moment and want to come to the mic to talk about it?

- Silence

- Gorry Fairhurst: I'm very appreciative of the changes you're making, this is improving, it's good. I'd love to see the interop bit, that would be cool. I have a couple of things that read oddly when I read the document. For example, "You MUST limit the buffering". That's a good MUST, what does it mean? If you exceed the MUST, then you drop, sure, that's cool. Can we define it better.

- Gorry: Second thing is the title. Is this ethernet and are these ethernet frames that we're tunneling? I feel that ethernet proxying will raise review comments, since there's lots of things in ethernet that we're not doing, we're just extending frames across something.

- Gorry: There's a whole section where we say it's not really ethernet, I don't understand how this works without broadcast and multicast? Can we say that you just have to send multicast frames. That feels like a big missing thing there that makes it ultimately useless to me.

- Alejandro: We're not saying that this needs to be explicitly handled, but you can carry the frame across just fine. I think that's what the draft is saying now. 

- Gorry: I think that's not what you're saying, but I think that's what you intend to say. I think there are things here that need to be tidied up before it gets wider eyes. I don't think this is a big thing, it's mostly Section 7 that needs to be more precise.

- Lars Eggert: I made the point earlier when adoption was raised about whether this was ethernet and if all the ethernet things were in it. The other thing that concerns me is the lack of interop, i.e. the lack of interest. Should we go experimental with this? We look for a fairly high bar for proposed standard.

- David Schinazi: I'm getting a lot of deja vu from the conversations around CONNECT-IP a few years back. Our intarea reviewer had some thoughts about what is a link or a router, etc. None of that led to changes in the protocol, but it did lead to clarifications in the document. I understood the doc the way that Alejandro intended it, Gorry if you could find issues, we can improve it.

- David: Wasn't there interop between you and Ericsson?

- Alejandro: A request was sent and a reply was received, and frames were carried.

- David: Let's try to get that one again. We have one implementation for sure, if we can get two that interoperate, that clears the bar for standards track by miles for most of the IETF, I'd like to keep this standards track if possible.

- Mirja Kuhlewind: We did this and stopped because it seemed like we had gotten enough to demonstrate that things were working. Gorry, most of your comments seem editorial to me, let's get some issues on GitHub and then fix them. We're happy if you can put a packet in on one side and get it out the other side. Whatever you do in your router you can do here, but you don't have to talk through all the cases in the draft. What you have is straightforward, minimal, and easy to implement. We can do new drafts for special handling if needed.

- Gorry: I think section 7 is basically benign rather than helpful, but it needs to be helpful. This is the place where I think we need to do it, we just talked it thorugh here, it's essentially a long cable that goes through a device that knows what to do with ethernet. We can't say that we connect broadcast domains and then say that we don't know what to do with broadcast. Other people have done this in the IETF, just copy the text.

- David: I'm not sure I understand.

- Gorry: Section 7 doesn't make any sense.

- David: Maybe some suggestion text could help.

- Gorry: This is not an AD comment. It's a comment from a group of people who have looked at these documents, this will come up, so let's fix it in the working group. 

- Mirja: Even if you think using ethernet is overclaiming, I wouldn't want to rename it, it fits the terminology that we're using for UDP and so on.

- Lars: In case you haven't, RFC4448 is ethernet over pseudowires. There are a lot of things that document touches that your document doesn't, and that text was there for a reason. Tunneling was the hot thing back then in the 2000s and 2010s, so lots of text around for how to do that. Pesudowire people did the work to figure out what it means to carry ethernet over a tunnel. Take a look to see if there's anything you can take.

- Alejandro: Read those earlier, will give them a second read.

- Lars: If you read it and didn't find anything in there, then mention the past attempts in the document and explain why you don't need them.

- Mirja: We did ask Intarea for an early review, did we get it?

- Dennis: We got it back recently, I think it was ready with issues, need to address the issues raised. Interop will help convince people that this is ready to go. We've reached out to the IEEE as well so we're doing what we can there, too.


## [DNS Configuration for Proxying IP in HTTP](https://datatracker.ietf.org/doc/draft-ietf-masque-connect-ip-dns/) - _Yaroslav Rosomakho_, 15 minutes

- David Schinazi: Up until PREF64 was invented, the technique for that was RFC7050 which relied on DNS to get the prefix. IPv6 folks realized that having a direct way to communicate it worked better, which is how we ended up here. It should be easy to add this, I would recommend adding this now.

- Ted Hardie: In reading the handling of DNSAssign in the draft, it's clear that the new one supersedes the configuration capsule. When you spoke there about the invalidation, that raised a concern. When you receive a capsule with new information, my assumption is that you do not invalidate any information that was associated with the previous one. Can we make explicit in the draft what the intent is? If you asked me, I would say do not invalidate previous entries. It's going to be complicated to track if you have to do that.

- Yaroslav: You're suggesting that the language should be "SHOULD NOT invalidate".

- Ted: Yes, supersedes configuration, but does not invalidate prior entries.

- Jonathan Lennox: I don't care what you do, but if you do PREF64 stuff, we should update the title and abstract so that people don't get confused.

- Dennis: How's the interop situation?

- David: Yaroslav has an implementation. I owe you one, I don't have it yet. I don't want to hold things up, but it would be good to get before WGLC.

- Yaroslav: We should also do interop with PREF64 if we do that.

- Dennis: That sounds great.


# As Time Permits

## [ECN and DSCP support for HTTPS's Connect-UDP](https://datatracker.ietf.org/doc/draft-westerlund-masque-connect-udp-ecn-dscp/) - _Magnus Westerlund_, 15 minutes

- Mike Bishop: This is an issue with how we do extensions in MASQUE and ECN just happens to be the extension that ran into it. I question if it needs to be in this document, but maybe it is what we need to fix.

- Benjamin Schwartz: I think there's some philosophical questions here. My core comment is that I don't think we can assign different context IDs to different ECN values, because context IDs are variable length, which could result in different effective MTUs for different ECN codepoints.

- Magnus: Can you just choose numbers carefully?

- Ben: Not really, we're asking applications to pierce the varint barrier when making context ID assignments. I think we should be careful about losing MTU, but here we're saving less than one byte.

- Yaroslav Rosomakho: It would be beneficial to design this in a way that is applicable to TCP as well. Traditional CONNECT there is no place for it, but templated CONNECT that uses capsules, we can now add DSCP if we feel like it. It seems good to avoid encapsulating it inside the context, but rather to have it as a separate field that we can use for TCP if we decide to do that instead of having to reimplement it for TCP. 

- Magnus: In my understanding, CONNECT is back to back TCP sessions across an end-to-end, and therefore this doesn't apply because we run it on an individual link.

- Yaroslav: Right, but templated CONNECT is using capsules to encapsulate data / segments.

- Ben: Just to be clear, CONNECT-TCP draft does not say that each capsule is a TCP segment, each capsule is an arbitrary contiguous section of the TCP data bytestream. If you define a new capsule, then sure, but the current implementation is just bytestreams and is entirely above the TCP abstraction.

- Marten: One thing to keep in mind is that CONNECT-UDP has the property that you can send datagrams with the CONNECT request and the proxy can drop them if it rejects the CONNECT request. Sending things optimistically requires both endpoints to agree on what the encoding is, which is a complication in this design space.

- Tommy Pauly: Thank you for doing this, it's good to enumerate all the cases. The capsule composition design reminds me of the things we talked about early on in the group. I think this is very expected. I don't see that there is a bug in how capsules and contexts work, we just need to explain how it is. 

- Tommy: I think this would work fine in front of TCP, but I don't think this is normally what you would do. Things that can chain on front would work fine with that. Not sure if that's good for ECN, but in general.

- David Schinazi: Thanks for doing this. I don't think this goes well with TCP because the DATA capsule does not respect packet boundaries and ECN and DSCP are a per-IP packet thing. I don't think we should go down that path.

- David: We discussed composibility early on in MASQUE and the mindset that we decided on was to not try to define a unified theory of MASQUE and only build things as we're using them. I think that was the right call, if we had designed this composibility at the time, we would have gotten it wrong. I still think that we're kind of in this boat, here. Right now we have two extensions. My recommendation is to have the ECN document say here's how I compose with the other one. When we have a third, then maybe we say "here's how these compose" and we keep improving it slightly with every document. 

- David: For ECN, I think we're going to have to find ways to negotiate which QUIC connections are using ECN or not, because on decapsulation it gets tricky if you do ECN instead of a drop for a congestion controller that isn't going to react to the signal.


## [The MASQUE Proxy](https://datatracker.ietf.org/doc/draft-schinazi-masque-proxy/) - _David Schinazi_, 10 minutes

- Dennis: Recharter to bring in scope for adoption. Also remove language on accepting new extensions.

- Yaroslav Rosomakho: If we're no longer accepting new MASQUE extensions, then the intent is that they go to HTTP and do we expect HTTP to have cycles for that? They have full meetings (twice) during each IETF.

- Lars Eggert: Agreed on the being busy. What do you envision a group doing?

- Yaroslav: We'd been exploring doing compression for CONNECT-IP. My implementation consumes at least 5% of CPU time when doing checksums. Would be great to be able to signal that you don't want checksums for this. Those are two examples that jump out at me.

- Lars: That was helpful. I don't know if we want to do this now or later, but finding out if people have other things they want to bring here.

- Tommy Pauly: (as httpbis chair) I think as some cases, we already see MASQUE-like things that have gone there, templated CONNECT-TCP is there and is very much in the spirit of this. It has capsules and everything like that. It is not in MASQUE, and I think that is appropriate. Work on the heart of capsules, sure that belongs in HTTP. There are also MASQUE proxy things that happen elsewhere, proxy config is happening in intarea. If there are things about CONNECT-IP tunneling and how we do compression, what is the right home for that? Is that an INT thing since it's with tunneled packets? It makes sense to have MASQUE be able to close.

- David: I agree, I think it'll be good to scope this down and close. I think scoping it down, compression is the only thing I can think of that is down the pike. We haven't deployed CONNECT-IP in production yet. We could list that as what we want to do before we close.

- Mirja: Can we wait until we have more? I have a compression draft on my todo list, but I didn't get a chance to do it before this meeting.

- Ben Schwartz: I think there's a divide between some things like WRAP-UP that are generic capsule functionality.
