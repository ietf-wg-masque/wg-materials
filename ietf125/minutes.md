These minutes were created with the assistance of generative AI, please double check anything critical with the Youtube recording.

-----

**[00:00 - 02:30]**
*(The video begins with the title slide and general background activity as attendees settle in.)*

> **Slide Title:** IETF 125 Shenzhen
> hosted by CNNIC
> Multiplexed Application Substrate over QUIC Encryption
> Thursday March 19, 2026

**[02:31] Marten Seemann:** So, let's get started, shall we? Looks like there are a lot fewer people than usually, and I see 36 so far, and not many in the room either. So if you're in the room, please log in to MeetEcho so that we can see you.

**[03:00] Marten Seemann:** So, this is MASQUE. We are doing a hybrid meeting. At this point in the week, you should probably be familiar with how this works. This is the Note Well, you should also be familiar with that; you agreed to it when you signed up for the meeting, so I will not spend any more time on this here.

> **Slide: Helpful Links**
>
>   * **Agenda:** [https://github.com/ietf-wg-masque/wg-materials/blob/main/ietf125/agenda.md](https://www.google.com/search?q=https://github.com/ietf-wg-masque/wg-materials/blob/main/ietf125/agenda.md)
>   * **Meeting Notes:** [https://notes.ietf.org/notes-ietf-125-masque](https://www.google.com/search?q=https://notes.ietf.org/notes-ietf-125-masque)
>   * **Chat:** [https://zulip.ietf.org/\#narrow/stream/masque](https://www.google.com/search?q=https://zulip.ietf.org/%23narrow/stream/masque)
>   * **Working Group Chairs:** Dennis Jackson and Marten Seemann

**[03:32] Marten Seemann:** And this is the agenda for today. First of all, do we have a note-taker?
*Marten confirms the note-takers are Marius Engelhart and Mirja Kühlewind.*

**[04:11] Marten Seemann:** So the agenda for today is we'll first give you a quick update on current items in the working group. And then we have presentations for the IP compression by Yaroslav, then David Schinazi will talk about the Architecture Draft, and then we have some time for the ECN draft. Any agenda bash? That does not seem to be the case, so we'll move on.

> **Slide: WG Status: Adopted Documents**
>
>   * draft-ietf-masque-connect-ethernet: Shepherd Write Up Complete
>   * draft-ietf-masque-connect-udp-listen: Shepherd Write Up Complete
>   * draft-ietf-masque-quic-proxy: See next slide
>   * draft-ietf-masque-connect-ip-dns: Ready for WGLC?

**[04:58] Marten Seemann:** First of all, an update on the connect-ethernet document and the connect-udp-listen. David and Alejandro have done the shepherd write-ups for each other's drafts, and they look good to the chairs. So we will move forward with those documents and forward them to the AD, who will then forward them to the IESG. I see a thumbs up from the room... Oh, that's David. Hello.

**[05:42] Marten Seemann:** Then we have a question for the authors of the draft of connect-ip-dns. Haven't heard a lot recently, but it seems like we might be ready for Working Group Last Call?

**[06:01] David Schinazi:** I believe we're still... we still want to do some interop testing. It's a fairly straightforward spec, but it's still I think a good thing to do interop testing. So if anybody has a prototype, would like to test DNS and/or NAT64 (Pref64 sorry I should say), please reach out to me and David.

**[06:29] Marten Seemann:** What's the implementation status?

**[06:33] David Schinazi:** Uh, I have one that interops with itself. But would be great to have more.

**[06:43] Yaroslav Rosomakho:** Yeah, sorry, I owe Yaroslav an implementation on this one and haven't had time. So that's kind of what we've been waiting for. If anyone has that opportunity as well, that would be awesome. But hopefully soon.

**[07:03] Marten Seemann:** Sounds good. And then we'll talk about quic-proxy next.

> **Slide: draft-ietf-masque-quic-proxy**
>
>   * Discussion at IETF 124 over security properties and intended stream.
>   * **Next Steps:**
>     1.  Authors are engaging with feedback
>     2.  SecDir Early Review will be requested
>     3.  Status to change from Experimental to Standards Track
>     4.  Second WGLC to confirm WG consensus

**[07:11] Marten Seemann:** So we did a Working Group Last Call on the quic-proxy document, and we got some feedback from Martin Thomson and Kazuho. So thank you for that. We will request a SecDir review, and the authors requested to change the status from Experimental to Standards Track. Once all of this is done, we would like to do a second Working Group Last Call to once again confirm the consensus in the working group.

**[08:00] Tommy Pauly:** Yeah, thank you. So just to comment on the engaging with feedback, as we mentioned to the chairs, some of the comments from Kazuho we have already incorporated onto the editor's copy and he's reviewed that. But a number of the other comments, and greatly appreciated, and I think as Marten had noted, they were kind of delayed reviews or things that had been discussed in the working group. At this point, and I just want to kind of make sure the working group hears this... I think a number of the things like essentially around the solutions for how we fix the loop problems and who picks the CIDs, that's something we talked about a lot... we don't expect to be making any large design changes to how we solve the loop problem.

**[09:21] Marten Seemann:** Sounds good. Can we expect a new document before Vienna then?

**[09:26] Tommy Pauly:** Yes, certainly.

> **Slide: WG Status: Proposed Work**
>
>   * **Upcoming Adoption Calls:**
>       * draft-westerlund-masque-connect-udp-ecn-dscp
>       * draft-rosomakho-masque-connect-ip-optimizations
>   * **Pending Recharter:**
>       * draft-schinazi-masque-proxy

**[09:38] Marten Seemann:** Then a quick update on the proposed rechartering of the working group. We discussed this at the last IETF meeting. We will run adoption calls for the two drafts listed here that we will hear presentations about today. Once these adoption calls have gone through, we will start the process of rechartering. The chairs will send a suggested new charter to the mailing list, and we can discuss it there. And once the working group has reached consensus on what the charter should be, we are ready to progress this new charter with the IESG. This will then allow us to run an adoption call on the masque-proxy document that David Schinazi has been working on. And that's all for updates from the chairs, and we can jump to the presentations. And we'll start with compression.

> **Slide: HTTP Datagram Compression**
> draft-rosomakho-masque-connect-ip-optimizations-01
> Yaroslav Rosomakho | Tommy Pauly
> MASQUE IETF125, March 2026, Shenzhen

**[11:38] Yaroslav Rosomakho:** Great. Hello everybody, my name is Yaroslav, and today I would like to present what was called connect-ip optimizations last time, now it's generalized and is called HTTP Datagram Compression. Tommy Pauly joined this effort as co-author, so now we have double enthusiasm and energy behind this proposal. First of all, I would like to give an executive summary... of what have changed in 01.

> **Slide: Notable changes in -01**
>
>   * Expanded scope beyond CONNECT-IP
>   * Added derived fields
>   * Aligned capsule naming and structure with other MASQUE drafts
>   * Added MTU limit for reconstructed packet
>   * Added option to specify maximum segments per template

**[12:25] Yaroslav Rosomakho:** First of all, expanded scope beyond connect-ip. Original proposal was very, very focused on connect-ip but apparently a number of proposed optimizations or compression techniques apply to things such as connect-ethernet, connect-udp and potentially they might apply to whatever other things might come encapsulated in HTTP datagrams. Added notion of derived fields... realigned capsule naming and structure. And added a couple more failsafes. So first MTU limit for reconstructed packets, so as a result of reinflation of the packet or decompression of the packet, the resulting packet would still fit into MTU on the egress. And added option to specify maximum segments per template so that a single template would not be ridiculously broken into too many small static segments.

> **Slide: Challenges with CONNECT-IP/CONNECT-ETHERNET implementation**
>
>   * MTU pressures when using QUIC datagrams
>   * CPU time lost for computing TCP/UDP checksums

**[13:37] Yaroslav Rosomakho:** So first, what this proposal is designed to solve. The primary, by far the most important purpose of this proposal is to reduce MTU pressures when using QUIC datagrams. So if you're using HTTP/3 datagrams that are encapsulated in QUIC datagrams, then you're not supposed to be doing fragmentation, and your MTU is smaller than 1500 bytes. And the smaller it is, the less efficient transfer that is. You obviously also need to whatever the application layer protocol is there to do proper MTU discovery or be adjusted to the smaller MTU that you have. And that is often a challenge... In my experience, 90 plus percent of all the troubleshooting issues associated with VPNs are MTU related. Second, slightly orthogonal but still important optimization that is offered here is checksum offloading. So if you're performing TCP UDP checksum calculation in CPU, even on modern CPUs, that's non-trivial amount of time that you would be spending... As I will demonstrate in certain scenarios, this is just a pure waste, computing TCP UDP checksum on one end and then ignoring them on the other end.

> **Slide: Three stackable mechanisms proposed**
>
>   * Templates to remove static segments from the payload
>   * Derived fields that can be computed by the receiver
>   * TCP/UDP checksum offload

**[15:15] Yaroslav Rosomakho:** So this proposal has three mechanisms. One is templates to remove static segments from the payload. Second derived fields that can be computed by the receiver. And third is TCP UDP checksum offload. So those mechanisms are stackable, meaning that you can create a template, you can define a set of derived fields and you can connect them.

> **Slide: Reusable templates**
> Many sections of packets of *the same flow* can be identical
>
>   * IP version
>   * IPv4 header length
>   * Traffic Class
>   * IPv4 ID / IPv6 flow label
>   * IPv4 fragmentation flags / fragment offset
>   * IPv4 TTL / IPv6 Hop Limit
>   * IP addresses
>   * Protocol / Next Header
>   * Source Port / Destination Port
>   * UDP header length
>   * Urgent pointer, some TCP options
>   * Potentially some headers on application layer

**[15:56] Yaroslav Rosomakho:** Reusable templates. So many sections of packets have many parts that are exactly identical. Typically these mechanisms are used per flow. So if you have a flow of packets... between source and destination, you will obviously have the same IP version, the same source destination IP, source destination port and lots of other boring fields in packets that would be exact same. So why don't we define a template and remove those fields from actual transferred data.

> **Slide: Splitting packet into segments**
> *(Visual diagram showing an Original Packet split into Dynamic and Static segments. These are assembled into a context ID specific template where each segment is prefixed with offset and length. Segments must not overlap. It is then shown being reduced into a "Deflated Packet" containing only the Dynamic segments.)*

**[16:35] Yaroslav Rosomakho:** Now the proposed mechanism is quite flexible, meaning that we don't define in this draft certain templates. We define a mechanism for defining templates by the sender. So sender chooses which parts of the packet would be repeatable... and removing those static segments from actual packets by defining context ID. We would expect most implementations would do that per flow... One of the feedback that Ben Schwartz provided on the mailing list is flexibility might mean that implementers will shoot themselves in the foot and will define some too specific templates. So we will provide additional guidance on how templates should be defined. But in some cases... sometimes you actually want a template that would apply to multiple flows. So if you have some kind of sensors that are sending UDP packets from random source ports... you might want to define a template that would not be using source port but only IP addresses and destination port. In some cases you might want to go into application layer.

> **Slide: Notes about templating**
>
>   * It's up to sender to decide how to split the packet. Receiver simply reassembles the packet. It does not need to be aware of internal packet structure
>   * Support of templates is announced by each party as well as limit of concurrent templates
>   * Packets can always be sent with context id 0 (without optimizations)
>   * Multiple templates can be defined for a single packet flow
>   * Single template can apply to multiple flows
>   * Templating can apply to any CONNECT carrying payload in datagrams and using Capsules for signaling

**[18:32] Yaroslav Rosomakho:** So it's up to sender to define how to split the packet... Receiver simply follows the template, reinflates the packet back and it doesn't need to be aware of why this template exists, what exactly the receiver is reconstructing. Each peer separately announces support for templating, and limits of concurrent templates that it can maintain, and also now we introduced a limit of how many segments per template receiver wants to have... Packets can always be sent with context ID zero, meaning that comes without any compression. Packets could always be dropped obviously, so if some abnormal packet comes that doesn't fit into template then sender has a choice. Sender can define either a new template, sender can try and transfer this packet with template ID zero if it fits into MTU, or sender can potentially drop this packet. Templating can apply to any connect carrying payload in datagrams, so today that would be connect-udp, connect-ethernet and connect-ip.

> **Slide: Derived fields**
>
>   * Some packet fields can be calculated by the receiver
>       * IPv4 Total Length / IPv6 Payload Length
>       * UDP Length
>       * IPv4 header checksum
>       * TCP/UDP checksums
>   * Requires understanding of packet structure
>   * Proposed IANA registry more fields could be added in the future

**[20:29] Yaroslav Rosomakho:** Second mechanism is derived fields. So certain fields in the packet can be calculated by the receiver. Most obvious ones are IPv4 total length, IPv6 payload length, UDP length, IPv4 header length and TCP UDP checksums. They of course require understanding of packet structure... and the draft now proposes IANA registry so that more fields, more derived fields could be added in the future.

> **Slide: TCP/UDP checksums offloading**
>
>   * TCP and (most) UDP packets carry internet checksum
>   * Checksum calculation can be computationally intensive
>       * \~5% on x64 and arm64 CPUs on a fully loaded CONNECT-IP tunnel
>       * Much more punishing on RISC-V architecture since it does not have add-with-carry instruction
>   * Wait... isn't checksum always set by sender? And don't we need to produce valid checksum to the receiver?

**[21:37] Yaroslav Rosomakho:** And third part of the proposal is TCP UDP checksum offloading. Now this is again as I said it's a little bit orthogonal to the previous one, so it's not about increasing effective MTU size, it's about not carrying unnecessary internet checksum. So TCP always carries valid TCP checksum, UDP packet in most cases carries checksum... Surprisingly enough, checksum calculation on CPUs is not as cheap. So in my tests on modern ARM64 and x64 CPUs when you have a CPU limited transfer of 1500 bytes packets and you compute checksums you lose about 5% of performance. And there are some other architectures like RISC-V that don't have add with carry instruction, so checksum calculation becomes even more expensive over there.

> **Slide: Checksum must be computed by somebody**
>
>   * Normally checksum is computed by NIC when packet is sent out
>   * But:
>     *(Visual diagram showing three scenarios:)*
>     1.  *Host Process originating flow -\> CONNECT-IP -\> NIC (There is no NIC to offload L4 checksum of original packet. It is computed on CPU by kernel or CONNECT-IP process).*
>     2.  *Host Tun/tap Userland process -\> CONNECT-IP (Tun/tap interface with GSO does not provide checksums. Without GSO tunnel interface is limited by \~500kkps).*
>     3.  *Host Userland process -\> CONNECT-IP -\> NIC with checksum offload.*

**[22:54] Yaroslav Rosomakho:** Now while of course in normal circumstances checksum is always set by sender, and in vast majority of practical implementations of anything that carries packets it's set by NICs... But if we have local process that is originating those packets, those packets are somehow intercepted by connect-ip client... then well there is no network interface card in the flow, the packet is getting encrypted, encapsulated into all the QUIC HTTP3 machinery... and then only getting passed into NIC that can take care of checksum of the outer encapsulated packet, but it cannot really look inside and help you with a checksum there. The second use case that I've run into is TUN TAP interfaces with generic segment offload...

> **Slide: Common way to signal checksum offloading**
>
>   * Process (or driver) and NIC agree on the capability
>   * TCP and UDP packets do include checksum of the pseudo-header
>   * Metadata structure associated with each packet includes
>       * Offset to the checksum
>       * Offset to the beginning of TCP/UDP header
>   * NIC calculates 1-complement and places the reconstructed checksum into packet
>   * Relevant APIs are available in XDP, Tun/Tap and other interfaces
>   * [https://www.kernel.org/doc/html/v6.17/networking/checksum-offloads.html](https://www.google.com/search?q=https://www.kernel.org/doc/html/v6.17/networking/checksum-offloads.html)

**[25:59] Yaroslav Rosomakho:** So the proposal is pretty simple is to signal that hey I'm capable of, I'm okay if you don't include proper TCP UDP checksum, instead the common approach is to include a checksum of the pseudo-header for TCP UDP and provide offset to the checksum and to the beginning of TCP UDP header that is used for recalculation of checksum.

> **Slide: Checksum offloading**
>
>   * Each party signals if it supports incoming packets with TCP/UDP checksum offload
>   * Sender indicates offloaded checksum when defining context id
>       * Checksum offset
>       * Start of checksummed data
>   * Packets still include checksum of the pseudo-header
>   * Applies to CONNECT-IP and CONNECT-ETHERNET

**[27:03] Yaroslav Rosomakho:** So again as I mentioned each party signals that it supports it, and it in practice can apply to connect-ip but could also very much apply to connect-ethernet as well.

> **Slide: Proposed Template Capsules**
> *(Visual showing capsule structures for TEMPLATE\_ASSIGN, TEMPLATE\_ACK, and TEMPLATE\_CLOSE)*

**[27:17] Yaroslav Rosomakho:** So in the latest version of the draft these are the capsules that we propose. So there are three capsules for templates, assign template is creating a new context with definition of static segments. There is a rule that segments must not overlap and that they must not touch...

> **Slide: Proposed Derived Fields Capsules**
> *(Visual showing capsule structures for DERIVED\_ASSIGN and DERIVED\_CLOSE)*

**[28:22] Yaroslav Rosomakho:** The second is derived fields, where again similarly you define context ID... and then you can provide list of derived field types.

> **Slide: Proposed Checksum Offload Capsules**
> *(Visual showing capsule structures for CHECKSUM\_ASSIGN, CHECKSUM\_ACK, and CHECKSUM\_CLOSE)*

**[28:44] Yaroslav Rosomakho:** And finally for checksum offload, similar structure of capsules except this time you pass checksum field offset and checksum start offset so that this could be passed to the network interface card or whoever will recalculate checksum on your behalf.

> **Slide: Future development based on feedback**
>
>   * Define recommended "safe" templates for typical use cases
>       * Reduce implementation mistakes resulting in MTU oscillation
>   * Checksum offloading and TCP/UDP checksum derivation cannot occur at the same time

**[29:02] Yaroslav Rosomakho:** So based on the feedback, unfortunately limited feedback that so far we've got on the list, the plan is to define recommended safe templates for typical use cases, so to reduce implementation mistakes... And we also need to notice that checksum offloading and TCP UDP checksum derivation cannot occur at the same time.

> **Slide: Next steps**
>
>   * Thoughts?
>   * Suggestions?
>   * Comments?

**[30:11] Yaroslav Rosomakho:** So, thoughts, suggestions, comments? I understand that's quite complicated proposal, lots of moving parts, but any questions including clarifying questions are very welcome.

**[30:34] Gorry Fairhurst:** Gorry, as an individual. You talk about IPv4 header length. But you don't talk about the IPv6 extension header chain. Are you intending to do IPv6 extension headers as well?

**[30:50] Yaroslav Rosomakho:** So we don't explicitly in the... let me go to the capsule definition... So in the template definition, it's abstract. It doesn't talk about IPv4 or IPv6 or anything else. It's up to you to define... depending on how you parse IPv4, IPv6, other headers. Or was your question about derived fields?

**[31:23] Gorry Fairhurst:** My question was what were you going to do with IPv6 extension headers? Were you going to support them in some way?

**[31:31] Yaroslav Rosomakho:** Right, so again, when it comes to template compression, yes, they are supported as anything any other static, repeatable header. Is there anything that you foresee that would need to be derived for IPv6 extension headers?

**[31:53] Gorry Fairhurst:** I'd need to think and I will read your draft and give you comments. I'd like to check the checksum stuff as well, but it sounds like you might be doing a good thing here. Thank you.

**[32:19] Alessandro Ghedini:** Hello. Based on some experience of software development I guess, sometimes optimizations make sense on paper but then when you try to apply them in practice they turn out to not provide a lot of benefit. So I wonder if you've had any chance of like actually implementing this and collecting numbers of you know improvements that you've seen in your implementations? You mentioned the checksum overheads on ARM processors so I wonder if you have any before and after results that you could share?

**[32:59] Yaroslav Rosomakho:** So I have for checksums as I said it's roughly 5%. So performance jumps in my tests from 11 to 12 gigabit per second per CPU core, so that's quite a bit in my book.

### 33:00 - 33:53
**Speaker: [Unknown Speaker]**
... templates, I've done very limited testing, but in my experience, this is more about compatibility with things that do not really like limited MTU, not as much about just raw throughput.

**Speaker: [Unknown Speaker]**
Yeah, I guess for compression, I would maybe like to see what the actual practical saving of bytes on the wire is versus not doing any templates.

**Speaker: [Unknown Speaker]**
Right, yes, and especially if your link is limited in terms of throughput and maybe lossy, so yes, that would be an interesting test. Thank you.

---

### 33:53 - 34:26
**Speaker: [David Schinazi]**
David Schinazi. Just wanted to say that I like this a lot, and I think the new version is even better than the, like, with this more generalized system. What happens, you've been warned, anytime Tommy shows up on a draft, it becomes a grand unified theory of something, but I think in this case, it's quite good. So yeah, I'm supportive of this work. I think it'll be useful, and I think we should adopt it in the working group.

**Speaker: [Yaroslav Rosomakho]**
Thank you very much.

---

### 34:26 - 36:13
**Speaker: [Unknown Speaker]**
Just checking, this reminds me very much of IP header compression, and is it capable of doing the same compression?

**Speaker: [Yaroslav Rosomakho]**
So the key difference, and I looked into quite a few IP compression schemes, it sometimes requires a little bit of archaeology. The key difference here, why this is unique, and why I feel that this needs to be reinvented yet again, is we have a very unique luxury of having a reliable channel where we define those templates and where we send those capsules defining those templates, and an unreliable channel where we transfer packets. All the mechanisms that I've seen previously, and it's perfectly possible that I've missed something, they are kind of in-band when you don't have those two separation, when you don't have reliable and unreliable channels. Also, here we have quite a unique thing called context ID that doesn't really exist in other places as far as I can tell.

---

### 36:13 - 37:37
**Speaker: [Unknown Speaker]**
Oh, I like that one. I was just wondering if the expression power of the template is enough to express the kinds of compression that you could do with older mechanisms. For instance, IP header compression had this thing where you could actually put in a sequence, you transformed an increasing number from a full number to a delta.

**Speaker: [Yaroslav Rosomakho**
Yes, however, here on the datagrams, we don't really have a similar sequence counter that we could derive other sequences from. And within a single QUIC packet, of course, you could have multiple datagrams at the same time, so you could have streams, so the QUIC packet number is not going to help, so yeah, I don't think these mechanisms are applicable. Now, in addition to static templates, now this new version introduced derived fields, so something that the receiver can just compute. It's perfectly possible that there will be other mechanisms in the future, and that's why next content ID, one of the reasons why next context ID is here. So if somebody in the future will come up with some other compression that can be applied on top of what is proposed here, that should be perfectly compatible with that.

---

### 37:37 - 38:09
**Speaker: [Unknown Speaker]**
Thank you.

**Speaker: [Yaroslav Rosomakho]**
Thank you.

---

### 38:09 - 39:25
**Speaker: [Mirja Kühlewind]**
Mirja Kühlewind. So I definitely support adoption. I think this looks good to me. The new proposal is on the one hand more generic, on the other hand can also be a little bit simpler if you have these static fields. So I think that's actually good. But I think that's a general point about like how generic we want to be, because that creates more complexity, and extensions are actually quite cheap, so we could define as many as we want.

---

### 39:25 - 40:11
**Speaker: [Yaroslav Rosomakho]**
Right. So again, one of the reasons why this proposal is more generalized in this version, I really didn't think about Connect Ethernet when I presented it, and Ali came after the meeting and said, "Hey, what about Connect Ethernet?" And I realized, yeah, it probably would be good to have it generic enough, maybe, you know, some people of the future will do Connect SCTP or something else, would be good to have exact same machinery.

---

### 40:11 - 41:43
**Speaker: [Mirja Kühlewind]**
I mean, I didn't make up my mind, but you can always create, you know, a separate extension for Connect Ethernet or whatever you have in future as well, and just keep it simple at the moment. But I mean, that's something we can discuss further, and I don't know what the right answer is. I have two quick, more detailed questions. One is, I see now that you actually put the next connection ID field in here. We discussed this last time, and I think we need to like agree on a general solution that we use for all extensions, the same solution, so that's probably a separate topic.

**Speaker: [Yaroslav Rosomakho]**
Right. And again, this is something that we should discuss, but there is also here a pattern, right? So we have ASSIGN capsule, type length context ID next context ID, we have ACK capsule, we have CLOSE capsule, which are absolutely identical, the only difference is type ID, so maybe we should define some kind of context ID, something something, that everyone will follow, and maybe even connect UDP bind people that currently don't. But yeah, that's something that we can...

---

### 41:43 - 42:46
**Speaker: [Mirja Kühlewind]**
It kind of was my next question, because that seems also, the assign and ACK capsule, that seems to be a pattern that shows up very often, and I was also wondering if we should use that in a more generic way. And about the close one, I think like, because this is probably because you have the maximum number of templates, so you also need to remove them, but you could also have a moving window or whatever. So again, there are many options. I don't want to make it complicated, but like, you know, not defining a different mechanism for every extension we have for all these kind of things would be useful, I think.

---

### 42:46 - 43:53
**Speaker: [Yaroslav Rosomakho]**
Yeah. And again, sometimes you have something, you know, long-standing and sending packets very rarely, but super important, you know, like signaling. So having an explicit close, I think, is better.

**Speaker: [Mirja Kühlewind]**
I mean, it's like, that's not the cheapest solution, but it's also not very expensive. You can always start a new connect request in parallel. You have multiple streams or whatever, and you can have a higher level... Like, that would keep the extension simple and would optimize for the norm case where you like don't need many templates or whatever. So that's another option.

**Speaker: [Yaroslav Rosomakho]**
Sure, yeah.

---

### 43:53 - 45:18
**Speaker: [Marcus Ihlar]**
Marcus Ihlar, Ericsson. I think this is great work. I think it's a big improvement and I really support this way of doing it. I like the idea of having some safe profiles. I think that's going to be really important, at least initially when we start deploying this. My question is, are we going to maintain some form of registry, or how do we document this? Is it the IETF who are going to own these profiles? What happens when we need to define new profiles? Are there any thoughts around that?

---

### 45:18 - 46:18
**Speaker: [Yaroslav Rosomakho]**
So right now, the way I imagine this is template profiles would just be example templates in the draft. They won't have any kind of identifier, they won't have any kind of official standing in any registry. This is something that authors of this proposal recommend implementers as a reasonable starting point. The only new registry that this proposal has right now is derived fields. Again, right now all the things are very IP-centric. Again, maybe in the future there would be other derived fields for other protocols.

---

### 46:18 - 47:00
**Speaker: [Marcus Ihlar]**
Okay, yeah, I think that makes sense. It will be interesting to see later on if we need to, you know, if we start compressing different protocols, how we handle that, but maybe we can take that as it comes up.

**Speaker: [Yaroslav Rosomakho]**
Right. And it's perfectly possible that it won't be within MASQUE working group, right?

**Speaker: [Marcus Ihlar]**
Yeah, yeah, sure, sure. Okay.

---

### 47:00 - 47:18
**Speaker: [Unknown Speaker]**
Anyone else? I have no idea how we're doing on time. There is no timer here, so...

**Speaker: [Dennis Jackson]**
I think we need to wrap up shortly on this.

**Speaker: [Yaroslav Rosomakho]**
Okay. I think we're looking about right. Well, yeah, thank you very much, everyone.

---

### 47:18 - 47:49
[Slide: Next steps]
* Thoughts?
* Suggestions?
* Comments?

**Speaker: [Dennis Jackson]**
Thank you.

---

### 47:49 - 48:21
**Speaker: [Marten Seemann]**
Thank you for this presentation. And then I'll move on to the next item, the MASQUE proxy document. David. How are we looking? Yeah, that works. Can you present my slides?

**Speaker: [Dennis Jackson]**
Yep.

**Speaker: [Marten Seemann]**
Thanks.

---

### 48:21 - 49:15
[Slide: The MASQUE Architecture]
draft-schinazi-masque-proxy
IETF 125 – Shenzhen / Tokyo – 2020-03-2210
David Schinazi - dschinazi.ietf@gmail.com

**Speaker: [David Schinazi]**
Hi everyone. I'm David Schinazi, still a MASQUE enthusiast, and here to talk about the MASQUE Architecture document. It's still currently called draft-schinazi-masque-proxy, but assuming based on how this discussion goes, we might rename it to draft-schinazi-masque-architecture. So, next slide, please.

---

### 49:15 - 50:11
[Slide: History of MASQUE, part 1]
* Started off as its own protocol that did everything
* Formed a WG
* Split into incremental changes to HTTP
* Enthusiasm intensifies
* Published a bunch of RFCs that don’t have MASQUE in the name

**Speaker: [David Schinazi]**
Quick history, and some of you might recognize some of this. We discussed this exactly a year ago in Bangkok. But more importantly, we started this whole nonsense seven years ago in two days, back in Prague, and realizing that I really haven't changed my draft templates since then. But so, when we kind of... the original proposal for MASQUE had its own protocol that like did a bunch of things. It like pretended to be HTTP and then wasn't.

---

### 50:11 - 50:54
**Speaker: [David Schinazi]**
But once we kind of created a working group, it became a list of small extensions to HTTP. And through a series of very enthusiastic contributions and discussions, we eventually published a bunch of RFCs. But none of them have MASQUE in the name, or almost... probably not really in the text either. I think it's only in the acknowledgments section. Next slide, please.

---

### 50:54 - 51:52
[Slide: Fast Forward a Few Years: A Common Conversation]
Application Enthusiast – I need to query a bunch of information anonymously
Privacy Enthusiast – Use MASQUE
Application Enthusiast searches online, finds draft-schinazi-masque from 2019
Application Enthusiast – Huh?

**Speaker: [David Schinazi]**
What happens regularly to me, and I've heard to folks at other companies as well, is you have someone on a product team that's working on their application, their privacy reviewer tells them they need to protect the user's privacy so they shouldn't... when they query that data, the server shouldn't know which user is querying it. And in some cases, people say you should be using MASQUE, and the application developer searches for MASQUE and gets really confused because they find this document from 2019 that is completely disconnected from the reality of what we shipped. Hilarity ensues, but it's not ideal. Next slide, please.

---

### 51:52 - 52:10
[Slide: This draft]
Answers the question: What does use MASQUE mean?
Privacy Enthusiasts can now send a link
[Image: A badge with the MASQUE logo and text: "What's in a name? That which we call a rose By any other word would smell as sweet"]

**Speaker: [David Schinazi]**
So, I wrote this draft initially a few years back, of... it kind of tries to answer the question of "What is MASQUE?". And the second feature of it is that you can send people a link to this document to have a single reference there for what MASQUE is. Next slide, please.

---

### 52:10 - 52:40
[Slide: Referenced by RFC 9750 (MLS Architecture)]
Recommendation: In the case where privacy or anonymity is important, using adequate protection such as Multiplexed Application Substrate over QUIC Encryption (MASQUE) [MASQUE-PROXY], Tor [Tor], or a VPN can improve metadata protection.
[Image: A video game character from Zelda receiving a MASQUE logo shield from a character saying, "IT'S DANGEROUS TO GO ALONE! TAKE THIS."]

**Speaker: [David Schinazi]**
The MLS architecture RFC, that got published last year, actually references this document. And it mentions, you know, when you're fetching this thing, you should use something to protect their privacy, such as MASQUE, Tor, or VPN. But that to me is kind of... shows that there's value in publishing this as an RFC. So, it'll be nicer to refer to an RFC instead of to drafts. Next slide, please.

---

### 52:40 - 53:17
[Slide: What’s New? Proxy Architecture]
[Image: MASQUE Proxy text changing to MASQUE Architecture]
[Image: Abstract AI generated illustration with glowing shields, cloud symbols and the MASQUE Architecture title]

**Speaker: [David Schinazi]**
So, what actually changed in the draft since the last IETF, or since I presented this a year ago? The main thing is AI has gotten a lot better at making dumb slide illustrations. Though if you look closely, it mentions AI and blockchain as part of this thing on the right, which MASQUE does not do. Anyway, still hilarious. Thank you, Gemini.

---

### 53:17 - 54:11
[Slide: What’s in the draft?]
[Image: Brad Pitt saying something]
* A bit of history
* Architectural principles (HTTP substrate, capabilities, privacy)
* Privacy properties (from websites, networks, partitioning, obfuscation)
* Related technologies (OHTTP, DoH)

**Speaker: [David Schinazi]**
But the big change is instead of focusing on what a MASQUE proxy is, we focused on what the architecture of MASQUE is, which was a suggestion from Lucas and from Dennis if I remember correctly. Alright, next slide please.

So what's in it? It gives a little bit of history. It talks now about architectural principles. Like, one of the core principles of MASQUE that wasn't discussed before is the fact that we're running over HTTP. And part of the benefit of that is some privacy, because it makes it look like web traffic, but you also get a bunch of other benefits, like you can get through load balancers, and it goes into that detail because in my mind, what made MASQUE really successful was the fact that it was easy for CDNs to deploy because it was over HTTP. So the document talks about that a little bit.

---

### 54:11 - 55:12
**Speaker: [David Schinazi]**
Also what privacy properties we have, and it talks about related technologies, like OHTTP and DoH. Sebastian just filed an issue that we should talk about CONNECT as well. I think like CONNECT isn't MASQUE per se, but it is definitely in the related technologies, so I agree and I'll add that to the document soon. Next slide.

---

### 55:12 - 56:18
[Slide: Next Steps]
* Adoption would require a recharter
* We’re planning on rechartering anyway
* Proposed new charter includes architecture
* Does the WG prefer this direction?
[Image: Same abstract AI generated illustration with glowing shields, cloud symbols and the MASQUE Architecture title]

**Speaker: [David Schinazi]**
What's next? We have this great beautiful future for MASQUE. The... like we mentioned in the chair slides, it's not technically in our charter, but the good news is we were planning a recharter anyway, in part for this, but also to kind of start winding down the working group and mention these last final extensions of other MASQUE documents that we're finishing. So the actual new draft fits better into the recharter than the one before because I think it mentions an architecture document. So my main question for the group here is, have you kind of read the latest one? Does this direction of talking about architecture instead of the proxy sound better to you? And if yes, I'll rename the draft before asking for adoption. Alright. Jaroslav.

---

### 56:18 - 58:19
**Speaker: [Jaroslav]**
Yes, I read this draft and I think this is a very important document, architecture document to tell to people what is MASQUE. One thing that I think is missing, and I don't know if it belongs here or it should be a separate document or something else, is there are so many flavors of MASQUE out there. So you have CONNECT, HTTP CONNECT, which is kind of MASQUE-ish. You have templated connect TCP, you have connect UDP, connect IP, connect Ethernet, and extensions of connect UDP. And you can do that over HTTP/1, 2, and 3. So when somebody says "I support MASQUE," what exactly does it mean? It would be good to I think... this document could be extended to have an overview of all things MASQUE with some recommendations what you should and should be using and what kind of things you should be avoiding if possible.

---

### 58:19 - 58:55
**Speaker: [David Schinazi]**
I think that's a great idea. The document goes into that a little bit in the capabilities section, but I like your idea of having maybe recommendations of, oh, if what you're trying is to proxy all your web browser's traffic, then connect UDP and connect are a great choice. But if you're trying to replace your IPsec VPN, then connect IP is a better choice. Things like that sounds like it would totally fit in this document in my mind. Thank you. Can I ask you to file an issue on the, on the GitHub Jaroslav?

**Speaker: [Jaroslav]**
Sure, yes. And I might even contribute some text if you don't mind.

**Speaker: [David Schinazi]**
Oh, even better. Thank you very much. Lucas.

---

### 58:55 - 60:11
**Speaker: [Lucas Pardue]**
Hello, yes. I have not had a chance to read the document. I'm sorry. But before Jaroslav talked I was like, is it singular or plural? Is it MASQUE architectures as in, you know, there's different ways you could build things using MASQUE? Seems kind of like it. Again I don't want to bikeshed a name, but are we just talking architectures, are we talking like taxonomies of how to put these pieces together, or terminology? I hate those just terminology documents, they're pointless, but coming up with a way to enable people to talk about the different ways to instantiate MASQUE I think is the most powerful thing, like... we talk about CONNECT a lot, but I just posted a link in the chat, there's a thing called the CONNECT protocol, right? And it's got nothing to do with any of the CONNECTs that we use. It's for doing some RPC over HTTP. It's really confusing to anyone outside this little sphere of our world when we're talking about things, and actually the fundamental protocol aspects are really simple. Like we're making it too hard.

---

### 60:11 - 61:31
**Speaker: [Lucas Pardue]**
So I fully, I fully endorse like this working group taking on board the document. I've got confidence we could figure out the right way to frame it. And I don't think it should take forever. I think we could like iterate on this and get something that's really done this year and just publish it.

**Speaker: [David Schinazi]**
Great, thanks. That makes a lot of sense. I think I'll have to noodle... on the best way to phrase that. I think starting with recommendations and then, but like yeah... part of those recommendations is like how you fit all those and how they interact is a good idea. So yes, I think that sounds good. Thanks Lucas.

**Speaker: [Lucas Pardue]**
And, and I'll, I'll read the document and come up with some more concrete things. It might be that you answer all of that already. I honestly, I can't say, so let me get back to you with some more concrete feedback.

**Speaker: [David Schinazi]**
Great. Thanks. Alright, that's all I got. Thank you everyone.

**Speaker: [Marten Seemann]**
And with that, we are ready to wrap up the session. A little bit early. Thank you to everyone, and see you again in person hopefully, in Vienna.
