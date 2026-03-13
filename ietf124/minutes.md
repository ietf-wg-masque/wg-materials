**[03:14]**
**Chair:** Good morning everybody. Congratulations for getting through the week and making it this far. We're gonna get started.

*(Slide 3: IETF Hybrid Meetings is briefly shown, reminding participants to use Meetecho for joining the queue)*
So those of you that are not yet in Meetecho, please do join to make sure that we get a room bigger than a shoebox next time. I see about eight people in there and I count way more than eight people in the room.

*(Slide 4: Note Well is shown)*
I imagine many of you are well familiar with this by now, but if this is your first meeting of the week, if you're just joining us for the day, then please make sure you're aware of the IETF Note Well and the conditions under which you participate in this meeting.

*(Slide 5: Helpful Links is shown, providing URLs for the Agenda, Meeting Notes, Chat, and listing Working Group Chairs Dennis Jackson and Marten Seemann)*

*(Slide 6: Agenda is shown)*
**Chair:** In terms of the agenda today, we're gonna spend a chunk of time just talking about the recharter and gathering some feedback there. We do not strictly need to use all of that time, would be very happy to get it back, but if there are things that we need to talk about and feedback we need to hear, it would be great to get that out and discuss that in person. And then we have a presentation from Yaroslav on the DNS draft and then two new drafts to be discussed.

*(Slide 7: WG Status: Adopted Documents is shown. The slide lists the following:)*

* `draft-ietf-masque-connect-ethernet` (Ready for submission to IESG)
* `draft-ietf-masque-connect-udp-listen` (Ready for submission to IESG)
* `draft-ietf-masque-quic-proxy` (Ready for submission to IESG)
* `draft-ietf-masque-connect-ip-dns` (Nearing WGLC)

**Chair:** In terms of the status of the working group, congratulations to the authors, because Ethernet, UDP listen, QUIC proxy, these are all ready to go to the IESG. They've completed their working group last call, and the indication from IP-DNS is that we're very nearly there as well, as we'll hear later in the presentations. Tommy?

**[05:00]**
**Tommy Pauly (Apple):** Hello, Tommy Pauly, Apple. This is a question from the authors of the QUIC proxy draft. We just wanted feedback for the status there, right, because the document is, and was originally adopted as Experimental. We just wanted to confirm is that what we want to have it be? As we were talking with people who had done hackathon work on it and implementations, they were like, "Eh, this kind of feels like we actually deployed this at scale and we base this on actual engineering work, it could be Proposed Standard." I don't think we have a particularly strong opinion, but we'd just like to get feedback, because this moment when we're doing the shepherd write-ups and everything is like, that's the right time to make sure it has the right status. So, would love to hear your feedback and anyone else's feedback.

**[05:57]**
**Mirja Kühlewind (Ericsson):** Mirja Kühlewind, Ericsson. Yeah, I think it's a good point. I think it's well tested, even partly deployed. So there's no reason for Experimental and that would make it easier to just go straight away to Proposed Standard.

**[06:12]**
**Marcus Ihlar:** Marcus Ihlar. I just came up to say exactly the same thing as Mirja. I think there is no reason to keep this one as Experimental. We have interop experience, there is deployment experience. Let's make it a Proposed Standard.

**[06:28]**
**Chair:** Yeah, that sounds good. We'll confirm that on the list, but that seems pretty sensible. There are two questions in the chat. "What is the experiment?" and the second question is, "How confident are we in the crypto for that doc?"

**[06:47]**
**Tommy Pauly:** Mmm, that's a good point. So, for the experiment, that's kind of the point now of like, what is the experiment? Because I think we had a bit more of an experiment before of like, will this work, or like will it actually help on efficiency. And like, we've done the experiment now at this point. So it doesn't need to be. For the crypto bits... I think that's an interesting question. Like, I'm not a cryptographer, so I'm not in a position to be making grand statements there. But I think when we interpret the work we have done and the analysis we have done in terms of the goal of trying to obfuscate and scramble... I think as far as we know, and based on the work we've done which I think has been a pretty good analysis there, it is achieving that goal. I think the goal of that obfuscation is probably a lower bar than some other things where it's like, "Oh, this is trying to be the base encryption or authentication for any particular flow." But I'd be curious if people have opinions on what other bars they would like to see cleared. But I think it's in a decent state there for what it needs to be.

**[08:29]**
**Lars Eggert (Mozilla):** Lars Eggert, Mozilla. So, if we have question marks around the security properties, I feel uncomfortable making it a Proposed Standard. However, maybe we can ask the security area for an early review and tell them like, "Here specifically is what we would like you to comment on," and if we get a security review that says this is all right, I would feel much more comfortable.

**[08:54]**
**Tommy Pauly:** I guess just to contextualize the question marks. I think it's more that in this particular goal, because it's about how good are you at preventing traffic analysis, and the whole notion of traffic analysis is a bit more in the researchy area for like, what are all the defenses there, that it's just a harder thing to evaluate. I think for obfuscating and saying this is unpredictable data, I think yes, we can say it is unpredictable data. Beyond that, like how do you do all the right things... because we kept it out of scope to say padding and timing attacks. So that's where it gets complicated. And I'm not sure any review of SecDir will be like, "Yes, this is... you cannot say this is impervious to traffic analysis without a lot more work."

**[09:51]**
**Lars Eggert:** Right. Lars again. So, given that the answer is "we are confident there are no issues, but a lot more words that I am still trying to parse through," I think it doesn't hurt to ask them, right? But I think if it's researchy and if we don't know, and maybe then the status is actually accurate. Because we can always revise it later, right? But that's just me and I'm going to sit down after this. Like, I would feel more comfortable if we had somebody who knew this area tell us that they think it's okay, or if they can at least sort of point out the issues that may exist, and then we can decide, is this something that we believe is okay for a Proposed Standard or not.

**[10:37]**
**Mirja Kühlewind:** Mirja Kühlewind. First of all, I think it's very good to have a security review, but this is kind of the next step anyway, right? So we are behind working group last call, so it will go into IETF last call and get its security review. So I don't think it makes a difference. We did a lot of analysis around this and I think described this very well what the properties are of this protocol that you get or don't get. And there's always an easy way out, this is an optimization. If you have a scenario where you want to make sure you have the best security you can get, you just don't use it. You use old school mask, tunneling, and whatever. But I think a lot of these analyses which are more researchy are actually around proxies in general. Because the question about how you can map traffic and deanonymize in a proxy scenario or whatever, there's like a ton of research to do there. And this just adds a different angle to it. So, I don't think this is more researchy than all the other questions we have in this space.

**[11:45]**
**Martin Duke (Google):** Martin Duke, Google. I don't know what status it should have, but if we do keep it Experimental, I think we should write down in the draft what the experiment is. Whether it's like, we want more penetration testing or we want some sort of, you know, we think it's possible attacks may develop against this or whatever, or something else entirely. There should be words in the draft about that which I was not able to find by a very quick skim, if those exist.

**[12:21]**
**Antoine Fressancourt (Huawei):** Antoine Fressancourt, Huawei. As far as I remember when we were working on security considerations for the design team for MASQUE, we ruled out attacks related to traffic timing and traffic analysis and we focused on providing the formal structure that would allow someone willing to have packet padding and traffic timing to protect against an external observer to have the tools to have those security measurements. But structurally, if you look at the header, we made sure that no metadata could be used to obviously track the packet from one end to the other. So this is what I remember. And I agree that traffic timing attacks are more a research part of things. There is a wealth of literature about such attacks for systems like Tor, etc. Maybe this is something that we can write a draft about if you are interested.

**[13:43]**
**???:** So I don't see Mike in the room, so I thought I might... oh, is Mike here? No, okay fine. Spencer's in the list. Spencer first. Go ahead Spencer.

**[13:58]**
**Spencer Dawkins (Speaking remotely):** Yeah. I wanted to say, I understand the concerns about traffic analysis. That sounds to me like we're worried about an arms race, which is not a bad arms race to be worried about. Would that threat prevent us from recommending deployment at scale? And I think a reasonable question for me to ask at the same time is this better than what people have as an alternative for deployment today?

**[14:50]**
**???:** Take Dennis first. No, I'm at the back.

**[14:55]**
**???:** So, I was going to talk about the Experimental versus PS. And one of the concerns I would have is if we don't try and list the Experimental bit more clearly now that we're trying to address, then we won't clearly be able to articulate to SecDir whoever is going to do the security review what it is that we want them to confirm. So let's be clear about this because this is a difficult area because there are Tor, there's AP, there's a whole load of technologies in this space that do tunneling and proxying, and we're not trying to mimic that. We're trying to do something else. So we've gotta say clearly what we're trying to achieve here and ask the security directorate what to do. But if we are intending to take this forward as PS perhaps, then we should tell them that before we do the review. So don't start a working group last call saying, "Hey, we're Experimental, please give us a security review," because the review you get will be lightweight and won't be good enough for doing a last call for a PS. So please decide and ask that question to SecDir, "Is this good?"

**Mirja Kühlewind:** Well, okay, well even if we do a working group last call...

**???::** Yeah, okay, but we did a working group last call as EXP, yes?

**Mirja Kühlewind:** Exactly, so I'm... that's what I'm motivating here. I'm saying the working group should decide that they think the experiment is done, and this is what the experiment was, and therefore they would like to take this forward as PS.

**[16:26]**
**Chair:** Okay, so if I can, I think, you know, from no hats at this point, but it doesn't sound like we're doing an experiment. I think we're confident enough in this thing that we want it to be Proposed Standard. And I actually don't think that there's a different standard for security between those things. I think it's gotta do the thing that it needs to achieve the property that it claims to achieve. And it might be we need to do some wordsmithing there to make sure that that's clear to implementers. But I think that's something that can be done sort of in conjunction with Sec review. So that would be my proposed way forward. Assuming we have consensus, change the status, and then trigger those security reviews and make sure that those issues if any are addressed properly.

**[17:12]**
*(Slide 8: WG Status: Proposed Work is shown. The slide lists:)*

* An informational architecture document about MASQUE, e.g., `draft-schinazi-masque-proxy`.
* An extension to CONNECT-UDP for ECN and DSCP support, e.g., `draft-westerlund-masque-connect-udp-ecn-dscp`, `draft-seemann-masque-connect-udp-ecn`.
* An extensions to CONNECT-UDP and / or CONNECT-IP to enable compression, e.g., `draft-kuehlewind-masque-ip-compression`, `draft-rosomakho-masque-connect-ip-optimizations`.
* A definition of qlog nomenclature for existing MASQUE mechanisms.
* No one has put a draft forward, but interest has been expressed from multiple participants.

**Chair:** Okay. So we also have some proposed work that's coming into the group that we'll hear from later today. Just as a reminder, we've discussed the MASQUE proxy sort of architecture document in previous meetings. There was a general consensus that we would like to adopt such a document, but it's not currently in charter, so we are going to try and recharter to include that document. We have two talks later today on the compression and ECN, and then it's been discussed informally, something along the lines of a QLOG related draft. And we'll discuss this in a second because nobody's concretely put such a draft forwards.

**[17:52]**
*(Slide 9: Rechartering is shown. It reads:)*

* Motivation:
1. Bring architecture document in scope
2. Sunset the WG
'MASQUE is intended to be a short lived working group'
Masque was chartered in 2020.



**Chair:** So, in terms of the rechartering, the motivation is essentially twofold. One, we want to bring that architecture document in scope. And two, we want to start to sunset the working group. MASQUE was always intended to be a short-lived working group and it's been five years, so you know, relatively short in IETF time.

**[18:15]**
*(Slide 10: Mechanism is shown. The text says "The MASQUE working group is now in the process of completing its remaining deliverables:" followed by a numbered list of 8 items.)*

1. A mechanism for proxying Ethernet over HTTP
2. An extension to CONNECT-UDP that is specific to proxying QUIC
3. An extension to CONNECT-UDP that allows proxying bound sockets
4. An extension to CONNECT-IP that allows conveying DNS and PREF64 configuration
5. An informational architecture document about MASQUE
6. An extension to CONNECT-UDP for ECN and DSCP support
7. An extension to CONNECT-UDP and / or CONNECT-IP to enable compression
8. A definition of qlog nomenclature for existing MASQUE mechanisms

**Chair:** In terms of how we do that, we've put up a PR that's been available early this week. But really the core change is moving from like an open-to-various-extensions to scoping it down to these eight items. The first set are ones that we've already adopted and are working on, and we're just in the process of finishing up. They are staying in the charter scope in case the IESG bounces them back and we need to do some more work to finish them. We have the informational architecture document in point 5. And then these remaining three are very much conditional on the working group wanting to adopt them. Or they may or may not remain in the text, depending on our group's decisions.

**[19:01]**
*(Slide 11: Proposed Next Steps is shown. The list is: 1. Get feedback, 2. Run adoption calls, 3. Recharter.)*

**Chair:** And in terms of how we do this, we want to get the feedback from folks in the room today if there is any. We want to run those adoption calls. Then we'll finalize and recharter at that point. So very much adoption calls then recharter rather than the other way around. So do folks have anything they want to raise? Anything they want to flag at this point? Any concerns about this approach or these decisions?

**[19:35]**
**Zaheduzzaman Sarker (AD):** Trying to get in the queue. Now I'm in the queue. Zahed Sarker. So, the process I saw, like that this is you want to adopt something that you're not chartered to do?

**Chair:** No, sorry. So for... we can't consider the informational architecture document until we recharter. So that...

**Zahed Sarker:** But the process in the next slide you saw like, okay, run the things, run adoption call for what?

**Chair:** Yeah, sorry, that would be for ECN, for the compression draft, and the QLOG draft if somebody put something forward. The extensions you can adopt. Yes. The architecture document is to one side for now. But it is something we've talked about in previous meetings and sort of had a hum in favor of. But there's been no consensus...

**Zahed Sarker:** My suggestion would be let's have the rechartering discussion and see like the all rest of the things falls into that one. I mean there is no point of calling for adoption and then rechartering to make them stick there. I think if you were talking about rechartering let's recharter, think like you want to work with this and then you recharter and adopt those things. And you can do rechartering adoption at the same time if you want to. So, process-wise, that would be clear to me. That's what I'm saying. Okay, so that's helpful. Thank you.

**[21:03]**
**Lucas Pardue:** Hello. Yeah, I gave some feedback on GitHub overnight. Like, the way this was laid out was just super confusing for me, because half of those things are nearly done and like ready to go to the IESG, at which point they're kind of not really a working group thing anymore. Um, and the other half are brand new stuff I don't know anything about. Um, so, yeah, it just seems like we could make this a lot clearer by not mentioning the current things and actually rephrasing the intent or desire to work on the other stuff that's listed there, which might make it more clear what is the thing that we want to work on, what is the measure of success that can mean that we can conclude the working group when those things are delivered or not delivered because we went through the process of establishing working group consensus and at some point maybe we failed and decided not to work on that anymore.

**[22:18]**
**Martin Duke:** Martin Duke. So, I... if I understand correctly, there are four new work items here. It's not up to me, I suppose, but my guess would be that if we were to shut down the group, that maintenance of these documents would pass to HTTPBIS? Is that a reasonable assumption? There's various places it could go, right? I guess HTTP or maybe TSVWG. So my point is, if there's actually energy to do three or four new items, I think yes, we should recharter for those three or four items. If there's energy to do one or two of them, particularly if the one that there is, is frankly a sort of niche-like QLOG, I think it would be better to shut the group down and go to whatever inheriting group and run a couple drafts through HTTPBIS or TSVWG. As TSVWG chair, I don't think that's the right place, but maybe it is, I don't know. That's up to an AD. But I think if it's just one or two documents, there's not going to be enough energy in a room, and we should shut it down and go somewhere else. Thanks.

**[23:27]**
**Tommy Pauly:** Tommy Pauly. I agree with Lucas's comments. Just reading through the charter, I'd encourage us to not do the editorial cleanups and fixes to earlier paragraphs and stuff. Like, I agree the text looks better, but it just raises questions of like, why is it changing? And like, having seen the side where charters have to go through approvals and stuff, it's going to be a lot easier if it's like really minimal and it's like very clear at diff. For the point of listing previous work items, again, ask your friendly neighborhood AD. But I think we could essentially say anything that has been currently adopted and progressed is still in scope, like, you can do something without having to list out all the individual work items.

**[24:28]**
**Zahed Sarker:** Quick AD interrupt on that. I mean, when you send the charter to the IESG, if you decide to want to make one, then you have to include the milestones. So it's obvious what work you're doing, relevant really. There will already be milestones, and there might be milestones for things you propose to do next, either way. Thank you, yeah. I think the milestones is a better way to handle this. For Martin's point, I think the architecture document is its own little beast. But for the other ones, I think, well, I don't know, I'm not sure about QLOG. But for the other two in the middle, I think they are useful because of the precedent they set, that they are extensions here that are looking at how do we do things that want to improve efficiency or add tweaks, and they raise questions about the composition of extensions, which is something that from the very beginning when we were doing capsules has come up, and it exercises that point. And I think it is worth doing at least enough of that so that when other groups get to maintain these things, it is clear the right pattern and we've worked out the kinks in how we do the extensions. Because these topics are not new, we've talked about them since the moment we talked about anything in CONNECT-UDP and CONNECT-IP. So the desire to do them isn't going to go away, and we should figure out how to do them. But we should make it clear that once we figured out how to do them, and then you know someone comes along in two years and wants to add another little IP packet marking that they don't have to do it here, that there's a formula that they follow. That heck maybe even is just like you need something in an IANA registry with an expert review and you don't even need to go through a working group process.

**[26:54]**
**Chair:** Okay, thank you. So I think that's pretty useful feedback. We'll probably follow this up on the list I think, rather than further discussion here.

*(Slide 12: DNS Configuration for Proxying IP in HTTP is shown. The title changes to "DNS and PREF64 Configuration for Proxying IP in HTTP". Authors: David Schinazi, Yaroslav Rosomakho.)*

**[27:11]**
**Yaroslav Rosomakho:** Good morning everybody. My name is Yaroslav and on behalf of myself and David, I'm presenting today DNS configuration for proxying IP in HTTP. Except now it's called DNS and PREF64 configuration for proxying IP in HTTP. For those who might not attended previous IETF meeting or forgot, we discussed if PREF64 information should become a separate micro draft or micro specification or be added into this micro specification. And rough consensus as far as I recall was that we are adding it into this specification so it has a little bit more substance.

**[28:01]**
*(Slide 13: Quick recap... is shown. It reads:)*

* This is NOT a new way to transport DNS or perform NAT64
* This is about signaling DNS resolver **and PREF64** configuration over capsules to get on par with traditional VPNs

**Yaroslav Rosomakho:** So again, quick recap, this is not a new way to transport DNS or perform NAT64. That is just signaling a mechanism to share between peers what are DNS resolvers available, and also information about PREF64, that is what IPv6 prefix should be used for 6to4 translation.

**[28:26]**
*(Slide 14: Changes from -04 is shown. It reads:)*

* Added new PREF64 Capsule
* Similar to PREF64 from RFC8781 (Discovering PREF64 in RA)
* No lifetimes since sender can always send fresh Capsule reliably
* Multiple PREF64 prefixes MAY be included
* No PREF64 prefixes MAY be included (invalidate previous PREF64 configuration)

**Yaroslav Rosomakho:** The changes from previous revision. We've changed the scope of the draft, we kept the... we changed the title, but we kept the name for consistency. Added new PREF64 capsule. Similar... its design is very similar to PREF64 from RFC 8781, which tells how to discover PREF64 in routing advertisement. The only difference there is we don't need to do anything about lifetimes, we have the luxury of reliable transport for this information. So we don't need to artificially expire them or whatever. You can at any point in time send a fresh one if you want to update information about PREF64 prefixes. Multiple PREF64 prefixes may be included in a single capsule. Again, unlike routing advertisement, we have flexibility of being capsules as long as we'd like. And PREF64... if you want to remove PREF64 prefixes that you've advertised previously, you know, like stop... we want to stop doing NAT64 translation for example. You can just send a capsule with an empty list of PREF64 prefixes, invalidating previous prefixes effectively.

**[29:45]**
*(Slide 15: PREF64 Capsule is shown. It diagrams the packet structure.)*
`PREF64 Capsule { Type (i) = PREF64, Length (i), NAT64 Prefix (104) ..., }`
`NAT64 Prefix { Prefix Length (8), Prefix (96) }`

* Unlike RA encoding we have the luxury of a whole byte instead of 3 bits.
* Always 96 bits long. "Prefix length" determines where to insert IPv4 address as explained in section 2.2 of RFC6052.
* As far as we know nobody actually uses non-zero suffix but some people do use Prefix Length other than 96 bits.

**Yaroslav Rosomakho:** This is what our proposed PREF64 is. So, very straightforward, just length and number of NAT64 prefixes. Each prefix comes with prefix length as a single byte.

**[00:00]**
**Yaroslav Rosomakho:** ...one interesting gotcha here is that prefix is always 96 bits, and prefix length specify offset where you want to inject IPv4. This is a little bit weird. Most people do 96-bit prefix length, but we are told that there are some exceptions to that. There are some people who do non-96 bit prefixes and since other specifications of 6to4 translation allow such a thing, we follow it. We don't want to introduce additional restrictions that don't exist in other specifications even if they open up use cases that are seldomly used. And by the way down there it's a table from RFC 6052 that tells how to add IPv4 address into your IPv6 prefix depending on prefix length. It's a little bit non-obvious for some, for myself at least. We refer that as normative reference in our document.

*(Slide 6: Next steps is shown. It lists one bullet point: `WGLC?`)*

**[01:08]**
**Yaroslav Rosomakho:** So with that, we are working, David and I are working on our own prototype implementations. We would like to do a little bit of interop before formally calling for working group last call, but we feel that we are very, very close to working group last call. Anyone else is interested in interop exercise for DNS information signaling and/or PREF64, please do reach out. We'd like to try this out. Even though they are fairly straightforward specifications, it would be great to have actual experience before going to working group last call. Any questions, suggestions, comments?

**[02:04]**
**Chair:** Very good. Yeah, and I think we've got Yaroslav again.
**Yaroslav Rosomakho:** Yeah.

*(Slide 1: Reusable templates and checksum offload for CONNECT-IP is shown. Author: Yaroslav Rosomakho)*

**[02:28]**
**Yaroslav Rosomakho:** Alright, now to something slightly less obvious. Reusable templates and checksum offload for CONNECT-IP. I've got quite a bit of feedback since I've put together this draft. At the end I will share some interesting pieces of feedback that I've collected, but this presentation is primarily about mechanisms that is defined in -00 draft as was submitted before the deadline. So, this proposal brings in effectively compression for CONNECT-IP to help with some of MTU pressures, and also allows endpoints to avoid Layer 4 checksum—TCP/UDP checksum—calculation for IP packets. So let's look into why and how.

*(Slide 2: Challenges with CONNECT-IP implementation is shown. The slide lists:)*

* MTU pressures when using QUIC datagrams
* CPU time lost for computing TCP/UDP checksums
* To a certain extent these challenges apply to other IP-in-X encapsulations, but we have a chance to ease them in CONNECT-IP

**[03:23]**
**Yaroslav Rosomakho:** So there are two challenges with CONNECT-IP that I have discovered when I was doing my experiments. One, very obvious, MTU pressures. So if you are sending CONNECT-IP over public internet, you don't have luxury of 1500 MTU. You have lower MTU because of overhead, because of encryption tag, and all these things. And the more pressure on MTU you have, the more constraints you have on the underlying protocol. So saving some bytes in MTU always helps, especially if you have some kind of weird internal protocol that is not necessarily flexible in terms of MTU that it is comfortable with. And secondly, I discovered that in certain environments—and again I'll come to that in just a moment—there is non-trivial amount of CPU time lost computing TCP/UDP checksums, and that would be great to avoid this if TCP and UDP checksums are not necessary.

*(Slide 3: Reusable templates is shown. The slide lists:)*

* Many sections of packets of the same flow are identical
* IP version
* IPv4 header length
* Traffic Class
* IPv4 id / IPv6 flow label
* IPv4 fragmentation flags / fragment offset
* IPv4 TTL / IPv6 Hop Limit
* IP addresses
* Protocol / Next Header
* Source Port / Destination Port
* TCP header length
* Urgent pointer, some TCP options
* Potentially some headers on application layer



**[04:27]**
**Yaroslav Rosomakho:** So, let's first go into reusable templates. So there are many sections of packets that are identical of all packets in a given flow, or in majority of packets in a given flow. So things such as IP version, IPv4 header length, traffic class, TTL, hop limit, and so on so forth, they are identical. So it would be great to not transmit them over and over again.

*(Slide 4: Splitting packet into segments is shown. It diagrams an "Original Packet" divided into alternating "Static segment" and "Dynamic segment" blocks, mapped down to a "Deflated Packet" containing only the "Dynamic segment" blocks.)*

**[04:54]**
**Yaroslav Rosomakho:** So the idea is that sender could identify those segments—so on this illustration they are highlighted in orange—signal to the receiver that yes, those are packet segments that will be the same in some packets. So I'm not going to... I'm defining a new context ID, I'm not going to transmit those segments in datagrams that are associated with given context ID. I will just send you those deflated, so to speak, packets with these segments missing, and then it will be your job to reassemble the packet, insert those segments based on the offsets that I tell you so. One of the benefits of this approach... well obviously it's quite flexible. It doesn't have a hard binding to any properties of IPv4, IPv6, TCP, UDP or whatever, so you could use it for multiple purposes. Potentially you could compress stuff in Layer 7 if you wanted to. So for example, if you know your length of connection ID, or if it's QUIC inside UDP, you could do some interesting creative things as long as you're careful. And also on the receiver side, you don't really need to have any knowledge of underlying packet structure. You just follow the algorithm to reinflate the packet. Lars?

**[06:25]**
**Lars Eggert (Mozilla):** Hi, Lars Eggert, Mozilla. I probably have a very stupid question so I apologize. So why does this need to be a protocol mechanism rather than just running like a compressor on the packet stream?
**Yaroslav Rosomakho:** So the reason why it's not using existing IP header compression... there are two reasons.
**Lars Eggert:** Or any compression.
**Yaroslav Rosomakho:** Yeah, yeah. So first of all, all compression mechanisms that I looked into so far, they are in-band. They follow the same datagram... so in our case we would have to implement them inside the datagram. We have in CONNECT-IP rather unique luxury that we have two channels. We have reliable channel, well in case of HTTP/3 QUIC where you have separate datagrams, we have a reliable channel where we send capsules and define context. And then we have so called, you know, forwarding channel where we send datagrams where we would like to deflate packets. So that's the primary reason why it's a separate protocol level mechanism.
**Martin Thomson (Google, speaking remotely):** It's better because...
**Yaroslav Rosomakho:** It's better because we have reliability, so we don't care if there is any packet loss on the datagram side. We have again reliable stream where we define these things. The whole operation becomes much more predictable.

**[07:45]**
**Martin Thomson:** Yeah, so in response to Lars, a generic compressor in this would leak information potentially by the size of packets, so that's something that's potentially worth considering. This sort of arrangement doesn't have that because it's fixed chunks. So um, that's good. A question for Yaroslav. Is there a limit on the number of segments that the setup has? Can you dynamically set that?
**Yaroslav Rosomakho:** So the current proposal doesn't have a limit on number of segments, but it has a limit on number of templates that the party is happy to maintain at any given time. So when this capability is negotiated, one party could signal to another, "Hey, I do support templates, and I'm happy to keep up to let's say 20,000 templates concurrently," meaning that you have some protection against misbehaving peer who would define arbitrary number of templates.
**Martin Thomson:** Do you think that we also need to define a limit of individual segments, not only templates?
**Yaroslav Rosomakho:** I think you probably do. If you have a 1200 byte packet, it would be really unfortunate if someone sent you 600 segments. And it would just increase the cost of forwarding. So you probably do want something.
**Yaroslav Rosomakho:** Okay, thank you. That's good feedback.

*(Slide 5: Notes about templating is shown. The slide lists:)*

* It's up to sender to decide how to split the packet. Receiver simply re-assembles the packet. It does not need to be aware of internal packet structure.
* Support of templates is announced by each party as well as limit of concurrent templates.
* Packets can always be sent with context id 0 (without optimizations).
* Multiple templates can be defined for a single packet flow.

**[09:20]**
**Yaroslav Rosomakho:** Okay, so right, notes about templating. I think I covered most of these things. It's up to sender to decide how to split the packet. So again, receiver doesn't need to worry about parsing IPv4, IPv6 or whatever. It's again down to sender to be smart about things. Support of templates is announced by each party separately. So you could have one party supporting it and another not. So when you declare that you're supporting it, that means that you can receive context definitions accordingly and templates, but you're not going to send them if the other party is not supporting them. And if you have one of a kind packet that is not falling into the template but part of the same flow, you always can use context ID 0 so it's not going to go away. So again, if you have some kind of packet with urgent TCP or unusual combination of things—somehow TTL changed—then you can always either define a new template or use context ID 0. And multiple templates can be defined in a single packet flow, so nothing in the specification requires you to have just one template per flow, so you can have multiple if that's what you want to do.

*(Slide 6: TCP/UDP Checksum offloading is shown. The slide lists:)*

* TCP and (most) UDP packets carry internet checksum
* Checksum calculation can be computationally intensive
* ~5% on x64 and arm64 CPUs on a fully loaded CONNECT-IP tunnel
* Much more punishing on RISC-V architecture since it does not have add-with-carry instruction


* Wait... isn't checksum always set by sender? And don't we need to produce valid checksum to the receiver?

**[10:37]**
**Yaroslav Rosomakho:** So the next one is slightly less obvious, is TCP UDP checksum offloading. So some personal story: so when I was building my first prototype CONNECT-IP implementation, I run into a very hard limit of 5.5 gigabit per second. So no matter what I did, I couldn't get above 5.5 gigabit per second per core. After scratching my head for a little while, I figured out that the issue was in Linux tun/tap interfaces. Because each time you collect a packet from a tun/tap interface, you do a syscall. And you typically cannot do more than 500,000 syscalls, so 500,000 packets per second, roughly 5.5 gigabit per second. And a potential solution for that is to use GSO on tunnel interfaces. So with GSO you get those packet trains, you get large artificial packets that you have then to slice yourself. But you save on the number of syscalls that you're doing. But then when you do that, you lose checksums. So you have then to compute TCP and UDP checksums yourself on your process level before you send traffic in CONNECT-IP. So once I've implemented that, I've significantly increased performance, that was great. But looking at flame graphs figuring out where the next performance bottleneck is, I figured that I'm spending about 5% of CPU time computing checksums, which is actually quite a lot in my book. So, and that's actually on relatively convenient x64 and arm64 architectures that have add-with-carry operation that is very very efficient for computing internet checksums. And there are other platforms such as RISC-V that might not have this operation and will have even more performance punishment if they have to compute TCP UDP checksums.

*(Slide 7: Checksum must be computed by somebody is shown. It diagrams a process sending traffic through CONNECT-IP to a NIC, and another diagram showing a tun/tap interface sending traffic to a Userland process and then to CONNECT-IP.)*

**[12:47]**
**Yaroslav Rosomakho:** So taking a step back in summary, TCP UDP checksums... what are the situations when you may need to compute them yourself in software? So first of all, if you have process originating a flow, sending data to you, you picking it up on the same host, encapsulate that in CONNECT-IP, encrypt, do all the things, and then send out. Well, there is no network interface card where you can offload TCP UDP checksum calculation because the original non-encrypted packet does not cross a network interface card. So you have to do that inside your process, or kernel would do that for you, but kernel would still do that in software wasting some CPU cycles on doing that. And the second is what I've described: if you're working with tun/tap interfaces on certain operating systems on Linux for example, there are certain situations when you need to negotiate with a kernel not to give you TCP UDP checksums. So that will enable other optimizations such as Generic Segment Offload on the tunnels. So meaning that in order to send correct packet in CONNECT-IP, you do need to calculate TCP UDP checksum yourself in the userland.

*(Slide 8: Checksum may not be required on the receiver side is shown. It diagrams similar flows but on the receiving end.)*

**[14:04]**
**Yaroslav Rosomakho:** And then on the receiver side similarly, you don't always need checksums, right? So if you are delivering packets from CONNECT-IP into local process, well you have all the wonders of strong encryptions in QUIC, so you don't really need TCP UDP checksums to check for packet corruption. The QUIC layer does that for you very, very effectively. So you can just hand over data to the process. Again, if you're working with tun/tap interfaces on operating systems like Linux, where you can say that yeah I'm giving you packets without TCP UDP checksum, that's okay. And then if you're some kind of a router or a bridge where you decapsulate packets from CONNECT-IP and give them to a NIC, then again most NICs these days support TCP UDP checksum offload. So they will be very happy to compute TCP UDP checksum for you as long as you provide them with correct offsets where to place the Layer 4 checksum.

*(Slide 9: Common way to signal checksum offloading is shown. The slide lists:)*

* Process (or driver) and NIC agree on the capability
* TCP and UDP packets do include checksum of the pseudo-header
* Metadata structure associated with each packet includes
* Offset to the checksum
* Offset to the beginning of TCP/UDP header


* NIC calculates 1-complement and places the reconstructed checksum into packet
* Relevant APIs are available in XDP, Tun/Tap and other interfaces
* URL provided for kernel.org documentation

**[15:04]**
**Yaroslav Rosomakho:** So, common way to signal checksum offloading—I did not really find a good normative reference for that. I'm still looking. So the only piece of kinda somewhat normative documentation is Linux Kernel documentation. But in my experience that's a process that is used pretty much everywhere in exact same way. So once you agree with your kernel or NIC driver or whatever that you're okay with omitting TCP UDP checksums, then you still compute TCP UDP checksum of the pseudo header—so Layer 3 header with a very small pseudo header with protocol number and length. And you have... you need to provide a metadata structure—again it's operating system specific—where you specify where the checksum offset is and where the data begins for computing of the checksum. So the resulting process will run internet checksum calculation over that data and will insert correct checksum into a given offset. That's again in my experience that's very common mechanism how all network interface cards and tun/tap interfaces and other places signal checksum offloading.

*(Slide 10: Checksum offloading in CONNECT-IP is shown. The slide lists:)*

* Each party signals if it supports incoming packets with TCP/UDP checksum offload
* Sender indicates offloaded checksum when defining context id
* Checksum offset
* Start of checksummed data


* Packets still include checksum of the pseudo-header

**[16:26]**
**Yaroslav Rosomakho:** So the proposal is to introduce an optional capability in CONNECT-IP to do this kind of checksum offloading. So when receiver doesn't really need checksums, and when sender doesn't want to provide checksums, would be great to save CPU cycles on both ends.

*(Slide 11: Proposed Capsules is shown. It defines `CONNECT_IP_OPTIMIZATION_CREATE` and `CONNECT_IP_OPTIMIZATION_DELETE` capsules with their byte structures.)*

**[16:45]**
**Yaroslav Rosomakho:** So, now while these mechanisms—the templating and checksum offloading—they are kinda separate, and solving two separate problems, they both define potentially Context ID. So that's why, as of this version of the draft, I'm proposing a single capsule—right now the placeholder name is `IP_OPTIMIZATION_CREATE`—that defines a Context ID, has length of static segments in that, and optional fields for checksum field offset and checksum start offset if we are doing Layer 4 checksum offload. So the structure of static segments contains a list of segments. Each segment has an offset length and the payload. Segments must be signaled in order and they must not overlap.

*(Slide 12: Future development based on initial feedback is shown. The slide lists:)*

* Extend scope to all Datagrams (CONNECT-UDP and Datagram Capsules)
* How to combine this with ECN/DSCP Context id?


* Generalize optimizations to TLVs with IANA registry
* Sometimes it is beneficial to omit L4 checksums completely
* IPv4 header checksum may be omitted
* Other optimizations could be added in the future



**[17:39]**
**Yaroslav Rosomakho:** So, and there are headers defined how would you signal support of these capabilities. So... Lars?
**Lars Eggert:** Hi, Lars Eggert, Mozilla. So it's been a long time since I did something with IP checksums, but there is a way to recompute it based on the binary differences between two things. Can you not apply this here?
**Yaroslav Rosomakho:** This is exactly what is happening here.
**Lars Eggert:** Oh okay, alright, sorry.
**Yaroslav Rosomakho:** You are... you're still sending checksum of the pseudo header and offsets. And then the receiving party, either in software if they don't have anything to offload to, or they just propagate this information to the NIC.
**Lars Eggert:** That's not what I meant. So, when you have the full IP packet and you have a checksum because you're like a router, it comes in with a checksum, and then you take chunks out, you can recompute the checksum by adjusting for the things you took out of the packet.
**Yaroslav Rosomakho:** I'm not sure if I fully understand. You don't need to recompute the checksum over the remaining bytes. You can adjust the existing checksum based on the binary difference between the original packet and the new packet. Right, so so the checksum... this mechanism is not about static templates. It's about that one party does not include checksum of the whole packet. It includes only checksum of the pseudo header. And the receiving party ideally uses NIC offload or something.
**Lars Eggert:** Alright.
**Yaroslav Rosomakho:** Yeah, so so yeah, that's... and I know that's confusing. Sorry about that.

**[19:12]**
**Yaroslav Rosomakho:** Again the reason why I'm proposing that as a single draft is because both of these mechanisms define Context ID, and it makes sense to create Context ID as a part of a single capsule. But I'll discuss that in a little bit. So, future... potential future development based on initial feedback. So one piece of feedback was that this mechanism, or at least the templating mechanism or compression mechanism, could be useful not only for CONNECT-IP but for pretty much all the places that use datagrams. So for example CONNECT-UDP, maybe CONNECT-Ethernet, you could obviously compress away your MAC addresses and other things in the same way if you wanted to do that. Though the question is how do we combine this with ECN DSCP Context ID? So one problem that I want to avoid obviously, if you have multiple mechanisms that define Context ID and you do those mechanisms at the same time, you can potentially end up with packet corruption. So if you have one capsule defining Context ID and another adding some more information about the same Context ID, you have a packet arriving in between, then you have incomplete information what that Context ID means. Meaning that potentially really bad things could happen. So there are two potential approaches that I believe we could explore. One is to have a unified capsule that defines Context ID, and all the things that contribute to that Context ID comes as TLVs within that capsule. The second option that was proposed by David Schinazi is to have every capsule that defines Context ID takes as one of the parameters, uses another Context ID as a parent. So if you're for example defining a capsule that would do checksum offload that defines a Context ID, it could take as one of parameters a Context ID of a previous optimization such as compression for example. So yeah, but that's obviously still to be defined. But it would be great to have something that would allow additional optimizations that could be added in the future. For example IPv4 checksum compression. So current mechanisms that are proposed here do not allow for that because IPv4 checksum is not static, it changes because your packet length changes, and it's very easy to compute. This is something that some parties I'm sure would like not to transmit and save two additional bytes on the MTU.

*(Slide 13: Next steps is shown. It lists:)*

* Thoughts?
* Suggestions?
* Comments?

**[22:02]**
**Yaroslav Rosomakho:** So, that was quite a bit of material. Any thoughts, suggestions, comments?
**Chair:** Lauren?
**Participant (Floor):** Actually, we have some real scenario I tried to figure out whether this going to work or not. Because we have actually the 3GPP has introduced a feature called ATSSS based on this...
**Yaroslav Rosomakho:** Sorry, which feature?
**Participant:** Some feature based on this technology, using CONNECT-IP, CONNECT-UDP things. The problem now is for the session IP, so basically you know the very high level IP is going to talk through the proxy to the final things. But since for that feature it's going to have two paths between the... we call from the terminal equipment to the proxy or we call the UPF. And through multipath QUIC. So we are going to allocate two pairs of IP addresses. Okay, the UE on the terminal equipment, you're going to have two IPv4 or IPv6 addresses. On the UPF side at the proxy, you're going to have another corresponding, it's like a you know totally four. Now the total encap so far is like you have the session IP and depend on some user rule selection something URSP, it's going to select one of the multipath QUIC paths. So in that sense, you have like reusable templates saying something are identical, like IP addresses. Actually what I'm looking at, it may not be identical since you have two paths. So see, you have some packet, right, you have the session IP and it's supposed to talk to the server part through the proxy. But on the encapsulation stack, it has to go through one of the multipath QUIC paths. That is going to have its own IP. Either the first path IP or the second path IP. So that's not identical, you know?
**Yaroslav Rosomakho:** So, this is about compressing of things that are encapsulated in QUIC datagrams. As far as I understood you, you're talking about multipath QUIC that is above that.
**Participant:** Um... it's... it's not exactly the payload part, because I'm looking at slide four, the segment things...
**Yaroslav Rosomakho:** Right, so this is covering packet that is encapsulated inside CONNECT-IP.
**Participant:** Okay.
**Yaroslav Rosomakho:** As a multipath author I don't think there is any dependency here.
**Participant:** Let me think about it. I can talk to you offline, because that is a feature has been standardized in 3GPP for 5G already. Yeah, I don't know whether it's going to get impact. Yeah, thank you.
**Mirja Kühlewind:** Mirja Kühlewind, Ericsson. And this was pretty much what I wanted to say. Thank you for bringing this here, thank you for talking about your implementation experiences and the performance impact of that. I think if we actually want to make CONNECT-IP deployable we need to look into compression. I think there is a broad space of like how generic you want to be about this, how simple or complex you can do things, but I think we should start the discussion here and figure out where we want to be in that space.
**Yaroslav Rosomakho:** Great, thank you.
**Chair:** Thank you.

**[00:00]**
**Mirja Kühlewind:** ...of context ID, so somebody defines a new context ID and refers a previous one, inherits all the optimizations from that context ID. Or a more generic TLV capsule that have all the optimizations aligned in one thing and both parties would need to negotiate via headers what they are going to do.
**Yaroslav Rosomakho:** Okay, thank you. I'll read the draft.
**Mirja Kühlewind:** Thank you.

**[00:25]**
**Eric Kinnear (Apple):** Eric Kinnear, Apple. First of all, thank you for putting this together. It seems promising, particularly the MTU win. I did have one question about the templates' concurrency. I notice that you're expressing that in a header and there's like a fixed concurrency limit. Did you consider doing something a little bit more offset based to avoid like rapid reset style registrations and removals?
**Yaroslav Rosomakho:** Interesting. So do you foresee that one party could somehow abuse another party by keeping defining contexts and removing them, this kind of attack?
**Eric Kinnear:** Yeah. So we had something similar in the QUIC proxying draft where we register forwarding rules and remove them. And that's the path that we took there. So if you'd thought about it...
**Yaroslav Rosomakho:** I have not and yes, let's connect offline. I'd be very interested in learning more about this potential vulnerability.

**[01:29]**
**Tommy Pauly (Apple):** I mean, yes, there is an acknowledgment. But the client can close it, can do the registration and close it in quick succession.
**Yaroslav Rosomakho:** Oh by the way, the current draft does not have acknowledgment capsules and that's a mistake on my part that is going to be fixed.
**Tommy Pauly:** Thank you very much.

**[01:52]**
**Marcus Ihlar (Ericsson):** Marcus Ihlar, Ericsson. So first of all, I also want to thank you for doing this work. I think this is a really nice design and I think we should move in this direction. You did mention, I really like this side with the static and dynamic segments, but you did mention that there are a few fields that are dynamic but easy to compute that you might want to offload. Is this something you would want to consider in this extension or is this something that you would like to do in like separate specific extensions for specific fields? Like there are a few length fields and such that you could consider for instance.
**Yaroslav Rosomakho:** I think it's worth having all the reasonable things within one document instead of having an explosion of all sorts of micro compression documents, if that's your question. But yes, I'd be very interested in collating all those use cases and defining a scalable mechanism in one document. So things that come to mind are obviously IPv4 checksum field, yes, various length fields. And again there are fundamentally two ways how you can do TCP UDP checksum offload. You either still pass over the pseudo header checksum, or you omit it completely and let the receiver do computation of the pseudo header checksum. Both are I think valid approaches and some would prefer others versus one versus another. So this is something that we should specify.
**Marcus Ihlar:** Thank you.
**Yaroslav Rosomakho:** Thank you.

**[03:26]**
**Chair:** Show of hands, who has read the draft?

**Chair:** Ok relatively few people have read the draft, on the basis of that, we'll let folks digest the draft and follow up on the list.

**[04:55]**
**Chair:** Okay, and finally we have Magnus to talk to us about ECN and DSCP support.

*(Slide 1: ECN and DSCP support for HTTPS's Connect-UDP is shown. Authors: Magnus Westerlund, Marten Seemann, Mirja Kühlewind, Marcus Ihlar. Ericsson logo is visible.)*

**[05:15]**
**Mirja Kühlewind:** Probably should have told you that I'm presenting.
**Chair:** No worries.
**Mirja Kühlewind:** Yes. So Magnus presented this last time actually. And we can go to the next slide. Shall I take the clicker?
**Chair:** It looks like the clicker works.

*(Slide 2: Updates since last meeting is shown. It lists:)*

* Marten Seemann joint as author to work on a common approach
* Clarifications
* Renamed "ECN/DSCP" to "ECN plus DSCP"
* documented that optimistic sending of UDP packets is a design goal
* Rewrote the paragraph discussing congestion controlled tunnel transport
* Fix for client initiated context IDs



**[05:32]**
**Mirja Kühlewind:** And what happened since last time is mainly we had actually two drafts last time, so now Marten Seemann joined as author and we did some minor updates because mostly clarifications, renaming, talking about congestion control in a different way, and also fixed a mistake we did about client initiated context IDs. But we didn't make any big changes because we really want more feedback from the group about what we should do here.

*(Slide 3: Two Extensions is shown. It describes "ECN-zero-byte extension" and "ECN plus DSCP Extension" with examples.)*

**[06:00]**
**Mirja Kühlewind:** A quick recap, what we define is actually two extensions. One is what we call ECN-zero-byte extension. So here we just use four different context IDs to signal or to give the information about which ECN field or ECN code point you should put into the IP header. And so you don't... you just choose the context IDs, you signal them, and you don't have to put any other data than the payload into the datagram. And then the other option, which provides you both ECN and DiffServ support, actually adds another byte to the before the payload that puts the information in there. Yeah, so these are the two extensions.

*(Slide 4: Two Options to negotiate use of each Extension is shown. It shows "Request and Response Header" and "Capsules (for dynamic allocation)" options.)*

**[06:42]**
**Mirja Kühlewind:** And then for each of the two extensions we have also two ways to provide the information to you. Either you can do it within the HTTP header directly at the beginning, or you can use a capsule which is useful if you want to actually provide additional context IDs later during the connection. But in the kind of easiest use case, like when you use the ECN zero-byte header, you provide the three or four or actually three context IDs—because the zero you already have—at the beginning, and you're done and you don't have to do anything else. So we found it still useful to have the header because that's kind of the common case. But then there are also a couple of cases where you dynamically want to signal that and that's why we kept the capsules as well.

*(Slide 5: Issue #14: Acknowledgement capsules is shown. It asks:)*

* Add an Ack capsule?
* Enables the sender of the assignment capsule to start using the Context ID without risking loss
* Is that a generally recommended approach or just overhead?



**[07:26]**
**Mirja Kühlewind:** This is the issues we would like some feedback on... yes?

**[07:32]**
**Lars Eggert:** Lars Eggert, Mozilla. I was still shocked by the combinatorial explosion here, so it seems like we are using more options than bits to encode, which is sort of slightly worrying. Can we narrow this down? Can I like not like need to implement 17 different variants to encode?
**Mirja Kühlewind:** You can just not implement it, yes.
**Lars Eggert:** No, what if... I mean it's good to be exhaustive in exploring the design space, but maybe at some point before we publish we can like narrow it on some.
**Mirja Kühlewind:** I mean, I'm totally with you, right. So we had a discussion about should we remove the header, but I mean that's actually what you want in most cases, right. And it's like, if the overhead is really, having a slightly longer document, which is not very long, it's actually short, and then you have the choice and you can just do whatever is right for you. I don't know. But like input is welcome definitely, absolutely.

**[08:29]**
**Mirja Kühlewind:** So this discussion we just had. We were also wondering if it's valuable to add an ACK frame here in the capsule case actually. The thing that you gain from getting an ACK frame is like you actually know that the other one has the knowledge. And so when you then start sending after you got the ACK you know that your datagrams don't get dropped, they don't get delayed or whatever. So you know, it's a certain benefit you get from it. But it's really a generic question for kind of all the extensions we do I would say. Is it a good pattern to always have these acknowledgement capsules or not? Go to GitHub and put some comments in.

*(Slide 6: Issue #3 Indication of capsule support is shown. It asks: "Is it the right mechanism to use an HTTP header with no configurations to indicate the capability to support later configuration using capsules?")*

**[09:21]**
**Mirja Kühlewind:** This is a issue about should we use headers or not. We have an issue, you can put some comments in there Lars, but if there's further feedback I'm happy about this.

*(Slide 7: Issue #17 Which extensibility solutions to support is shown. It lists three options: 1. Chaining of Context IDs, 2. Encoding next Context ID in payload, 3. Define the combination of connect-UDP payloads explicitly in the extension)*

**[09:34]**
**Mirja Kühlewind:** And then the third issue is actually the big one we would like some input on, and it's also a little bit a generic case. Because there are various ways how you can extend or how you can combine different extensions. Because the context ID is used to like provide you an extension, but what if you want to use the ECN extension and the compression extension, or the ECN extension and UDP listen? Like how do you combine these things? And there's currently one way that's proposed in the draft which is basically you in your capsule where you announce your context ID you also announce something like a following context ID. And then that just means that kind of you chain your extension. If you I'll go into detail. The other one is you could have multiple context IDs in the packet itself. So that would for each extension you have, you would like add another context ID and that would like add additional bytes to every packet you send. Or you can actually define these combinations and then like for all the combinations use a different context ID and signal all these context IDs. So that's even more explanation, explosion.

*(Slide 8: Option 1: Chaining of Context IDs is shown. It gives an example of combining ECN Plus DSCP with Proxying Bound UDP in HTTP)*

**[10:47]**
**Mirja Kühlewind:** So what does it mean? So option one means, you have the context ID 0, which is just regular UDP payload. And then for example you have the proxying bound UDP in an HTTP extension which would add an address and you assign context ID 2. And then if you want to combine these two, you assign context ID 4 and you just say before the payloads you actually have two fields: one is the address and the other one is the DiffServ point. Or if you just want to use the DiffServ point, then you define another context ID 6 in this case and that's only this one. So that's how you can use... and that means you have to in the in the assignment of the context ID you somehow have to tell that this context ID covers both this context ID and kind of the other other context ID that also has the address. That's what we have currently in the draft.

*(Slide 9: Option 2: Encoding next CID in payload is shown. It illustrates appending extra context IDs in the payload itself.)*

**[11:37]**
**Mirja Kühlewind:** So the other option is the one where you put the context ID into the packet. That would look like this. You always have one context ID and then in the case of the UDP listening extension you have the address field. And if you now want to add the DSCP field, you just add another context ID and another field and you have another context ID and another field or whatever. So that's another option.

*(Slide 10: Option 3: Chaining ECN encoded in one combined Context ID is shown. It illustrates defining separate context IDs for every combination of ECN and proxy configurations.)*

**[12:00]**
**Mirja Kühlewind:** And then the last option is basically this: you only have one context ID, but you really have to signal and define everything separately in your draft. Yes, please.

**[12:10]**
**Yaroslav Rosomakho:** Right, and then of course you may want to combine context IDs with other extensions that define context ID, such as that I just presented. It feels to me that option one is by far the most scalable, extensible. However, I think that it would make sense instead of when you define a context ID, to also tell "hey and there is another one coming"—if I understood how it's currently defined correctly—do the other way around. Define context ID and always have like a parent context ID. So if there is no parent, you say it's context ID 0, that's the parent. And if you have... if you inherit properties of another context ID, you specify what context ID was the parent. Does that make sense?
**Mirja Kühlewind:** What's the difference?
**Yaroslav Rosomakho:** The difference is sometimes you don't know which optimizations are you going to define later down the line.
**Mirja Kühlewind:** But you can... it's just about chaining, it's just like the question is just like how you at the end how you in which position you put the additional information in the in the thing here.
**Yaroslav Rosomakho:** Right. I was thinking of chaining of context IDs with other... reasons to do context IDs. So for example templates.
**Mirja Kühlewind:** Yeah yeah, so the context ID might not add a field, that's true. But it should... it can anyway apply. Yes, you just say this is the next context ID, or the previous one, it doesn't really matter. I think it's just like there is a next one, and the next one could just point to another one and whatever. And you can chain them, right? It's just like you have to make sure everything can work together. Right, if you like have two different extensions that define two different compression methods, you cannot probably use them together.
**Yaroslav Rosomakho:** Correct, that would be a logical error. But then, again from a kind of like protocol processing perspective, it feels to me that looking up at a previous context ID than a future context ID is more natural.
**Mirja Kühlewind:** In this one, the context IDs are in the capsules, so you register it once, and then you know how to parse the packet, always in the same way. This one is where the context ID is in the packet.
**Yaroslav Rosomakho:** Yep.
**Mirja Kühlewind:** Maybe it makes a difference there, I don't know.
**Yaroslav Rosomakho:** Okay, okay, let's let's connect offline.

**[14:27]**
**Tommy Pauly:** Tommy Pauly, Apple. I think I generally prefer two because it avoids some of the ordering and negotiation issues. It allows more flexibility for what gets negotiated when. Like I don't know, you can imagine you have some extension where you... you had some original context IDs and then you want to update them, now you have a whole set of new context IDs and if you've already chained them earlier with some other ones, you now need to redefine all of those too. Or like if you want to have a particular extension, then you want to stop using it, then you want to start using it again, or combine it with a new thing, you have to like keep updating your whole set of context IDs, and there's just like a lot of state potentially there. And if you get out of sync it may be unclear which set of combos the both sides know about.
**Mirja Kühlewind:** I mean, if you get out of sync, if you see a context ID that you just don't know, you have to do whatever you do with context IDs you don't know. You delay the packet for a while and then throw it away.
**Tommy Pauly:** Yeah. But I guess it's just like it's just yet another piece of information to sync. Like so you may have independently, or the other side may have already received all of the individual bits that to know about the new state, but they haven't received what is the context ID for the cumulative combination of them. Anyway, so I didn't think about the implementation complexity here, but like the one thing that's very obvious is of course the bits overhead, right? And like here you have an overhead in every packet, and the other one you have to send like a whole bunch of capsules from time to time, but you don't probably send them that often. So that's a clear difference.
**Tommy Pauly:** Right. And I think, we've had this discussion in the working group before. I would lean towards maybe having the, like actually look at like what is the actual byte overhead? Can we tolerate it? But I think it's a lot easier to reason about some of these cases. It also depends on like how frequently you have them, right? Because if you have a packet that just...
**Mirja Kühlewind:** I just wanted to say, it depends on the extension right? If you don't know what the extension is, you don't know how frequently you change it.
**Tommy Pauly:** Right. Because I think with this model like you would essentially end up defining like there are types of capsules that can be prefixes that can just... this is additional information, like "hey this thing had an ECT1 marking on it", and it's kind of infrequent and you like just slap it on the front and then otherwise we don't worry about it. And then there are terminal capsules which are probably always going to be like the UDP payload or the IP payload. And so I think for that model it can work well, but yeah, if you have the exact same marking on every single packet then it's more annoying. But I still think it's like easier for cases where you want to be able to change the marking and you're not just doing the same marking for all the packets and you like never change what your fields are going to be.
**Mirja Kühlewind:** But I mean also, for all those cases where you only want to use one extension, nothing will change, right? It's just like all those packets where you have to combine more than one extension. And in case of ECN, you could say it's actually not all the packets, just a few of them.
**Tommy Pauly:** Right. So, as a compression apparently enthusiast, I obviously strongly support sending more capsules or bigger capsules rather than eating into MTU. So it feels to me that option one is still preferable. Now when it comes to explosion of capsules and context IDs, well the implementers should be reasonably smart and define... if we have a parent-child relationships so that we have parent context ID and multiple child context IDs that depend on that, we could just ideally send things that change less frequently as the parent, and things that might change more frequently, like for example ECN or smaller things as the child. So we could maybe have some text around it.
**Mirja Kühlewind:** So I think the point is here actually that if we decide we go for any of these solutions I think it would actually... and I don't want to make this overly complicated because we were punting on this point earlier, but I think it would be good to kind of make the decision for all the extensions we do. Because then you have the same mechanism, and then you can actually decide if you want to chain one on the other or the other way around, right? So then it's just an implementation decision.

**[19:04]**
**Yaroslav Rosomakho:** Absolutely.

*(Slide 11: Next steps is shown. It lists:)*

* Thoughts?
* Suggestions?
* Comments?

**[19:12]**
**Mirja Kühlewind:** Yeah. So this is my last... oh no, I mean this is not the last slide, but this was the last slide. We would like input, but we would also like to get the draft adopted.
**Chair:** I see Zahed.
**Zaheduzzaman Sarker:** Yeah, thanks. Thanks for the presentation Mirja. I mean maybe I didn't get one thing that I was looking in the draft, maybe I misunderstood things. So, I mean now we can do ECN, we can also say like "okay you can do ECN DSCP". But perhaps the proxy wants to do one of the things, like DSCP only. It doesn't want to do ECN. So how does it tell? What does it do? I mean, is it like it doesn't really provide any ECN information with the same capsule?
**Mirja Kühlewind:** You could save two bytes if you really want to optimize for that. But you not doing ECN means your IP header code point is always zero zero, and so the ECN DSCP option just provides you the whole byte and so the ECN bits have to be always zero. And that's all you have to do.
**Zaheduzzaman Sarker:** So, I mean basically, we are saying like, if you... I mean I kind of read through that one, it is not that clear in the document. Maybe we should do that kind of thing. I was going to open an issue but I think I got the answer. Thank you. And yeah, I have another point. So this is basically... I mean, the proxy when you are doing this, any of these extensions, is for both upstream and downstream traffic. So if your... or like what is the direction of this thing? You expect like, "Okay, this is for..."
**Mirja Kühlewind:** It's independent. So both of the sides can just tell "I want to use ECN", yes.
**Zaheduzzaman Sarker:** Yes. Okay. Yeah. Thank you.

**[21:05]**
**Participant (Floor):** So, sorry, just as a confused individual. Like this is all sending one byte, which doesn't usually change, and maybe one bit in that byte changes. And we have this huge repertoire of stuff, so I feel like maybe this is about something much bigger, as in how we do extensions rather than how we do ECN and DSCP which seems easy.
**Mirja Kühlewind:** Yes.
**Participant:** And are we any... I kind of read it, but then I got confused by all the options. Are we aiming that any receiver will be able to pick up any combination that the sender sends? Or are we...
**Mirja Kühlewind:** You mean combination of what?
**Participant:** Well I mean are we expecting the implementations to do all whatever the pattern we choose, so that we can just send the other end the packet? Or are we going to be setting up the context ID and making sure that the implementation is supported whatever we're doing?
**Mirja Kühlewind:** So I'm not sure I understand your question, but the answer might be that to use these extensions, any of them, you also need to negotiate them. That's why you need the header as well. So you can of course only combine those that you negotiated and both sides understand.
**Participant:** Yeah, I'm trying to figure out what the failure mode is when we get that wrong. But the failure mode is we throw everything away because we lose, we don't have a context.
**Mirja Kühlewind:** Yeah. So I totally agree that this kind of combining different extensions we need to think through it carefully from the beginning to the end. But I think we need it.

**[22:36]**
**Altanai Bisht (Apple):** Hi, Altanai Bisht from Apple. Was there any intention of having harmony between the QUIC outer tunnel and the inner flows at all? Because I see that this could land into a situation where there is conflict and then there are performance implications.
**Mirja Kühlewind:** For ECN you mean?
**Altanai Bisht:** Correct.
**Mirja Kühlewind:** I think the normal tunnel recommendations for ECN apply, but we have some discussions there. So it depends if your tunnel is congestion controlled or not. If your tunnel is congestion controlled, you would consume the outer ECN bits directly for your congestion control and you wouldn't put them in the inner packet. If you are a tunnel, like traditional tunnels today that are not congestion controlled, then you have to like put the marking into the inner packet before you forward it. But there are normal tunnel considerations that I think have been specified in intarea apply. And there's some discussion about this in the draft, yes.
**Altanai Bisht:** Okay, thanks.
**Mirja Kühlewind:** Thank you.

**[23:40]**
**Chris Box (BT):** Hello, Chris Box, BT. So just a general comment. I think it's really cool that you're working on this problem of how do we enable L4S for a MASQUE proxied connection. When I looked at the draft, I found it possibly because I'm not so close to this work, coming to it as someone relatively unfamiliar with it, I found it difficult to comprehend what exactly is being proposed. There are no diagrams that say we're going to do this, and we're copying from this to this. There's something in section 6.3 I think about how you use this, but ideally I'd like to see more helpful text in there, or diagrams that explain how it's supposed to be used. I mean it does refer to 9599, and which is... I think Bob was an author of that, and yes that does... that did help me understand a bit more about what we're doing here and the feed forward and up and things. But I think ideally yeah, it would be good to have some explanation in this document if possible.
**Mirja Kühlewind:** Sure, we can look into that. Yes, we were concentrating on the specification, but some guidance makes sense, yes.
**Chris Box:** Okay.

**[25:05]**
**Participant:** Just a clarification question here. So you are trying to standardize the ECN plus DSCP within the context ID part. But there are some organizations who are going to over... write... use that one to define their own context ID between the, you know, the next session part. So if you've got unfortunate case, they got the same context ID, how to handle that case?
**Mirja Kühlewind:** So I'm very unhappy that 3GPP took this route and they are negotiating context IDs out of band. We told them if you do that, you cannot combine them with any other extension and that's what they accepted. So that's the case. You cannot combine it with this extension unfortunately.
**Participant:** Okay. I probably see you don't like it. But things... I suppose vendors have implemented the feature.
**Mirja Kühlewind:** Yes, but like this was when we had the discussion in 3GPP, we brought up the point that you cannot combine this way with new extensions, and they accepted that. So that's the case, you cannot combine it with this extension.
**Participant:** Okay.

**Chair:** Thank you for the discussion, we'll do the same show of hands as before, who has read the draft?

**Chair:** 12 yes, 20 no, similar to the last show of hands. We'll follow up on the list. Thank you for the great discussion, I think that lead to some very useful feedback.

**Chair:** This closes MASQUE at IETF 124.
