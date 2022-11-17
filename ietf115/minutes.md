
# MASQUE Working Group Agenda - IETF 115

Administrivia (5 minutes)

Blue sheets / scribe selection / NOTE WELL
Agenda revision

## CONNECT-IP (30 minutes)

IP Proxying Support for HTTP - Alex Chernyakhovsky

WGLC? 
David: Draft was in good shape, good progress at last IETF. Text-wise, this seems good. Let's see if it actually works. Great at hackathon to see that design choices worked out better than expected. We realized that ICMP for signalling just works for free. Personally happy, editorial pass before we launch WGLC, but that's the only thing and there are not any open issues. Motivate people to read the draft, a few weeks for editorial work. 
Alex: Almost all issues at interop were "oh your firewall was screwed up" or "certificate invalid", not CONNECT-IP. 
David: One instance of "wrong capsule types, they changed"
Martin Duke: Thanks to everyone for work on this. Is this a good time to do an early intarea review. 
Lorenzo Colitti: I haven't read the draft (fun). This feels like a low layer thing, and one thing that we have in Android unfortunately is that people use BSD sockets extensively, there are a lot of weird warts and assumptions that applications make. Has anyone looked at how to match those system calls to this, like bind/connect. 
Alex: This is more like a VPN with an individual network device, so apps don't have to worry. One of our implementations just brought up tun devices and you set routes to go through that device and everybody just went that way by default. 
Lorenzo: So IP address and then you're good to go? 
Alex: Yes, and apps didn't need to know. Other alternative is when you do flows by yourself, like if you have a QUIC stack or something that is doing packetization directly. We did that as well, but that requires the app to be aware of that mode. Not great for TCP, would need userspace TCP for that, but this is by design. 
Lorenzo: The second mode, do we have some way to know if it's expressive to support something that is using BSD sockets. This is just a question, it's maybe okay if we don't do that. 
Alex: Short answer is we didn't do exactly that, but we are running a QUIC stack over this mode and it wasn't bad.
Tommy Pauly: WGLC sounds good on current version, don't think we need another pass. Agree that doing intarea review in parallel would be good. Would be nice to do a capsule interop with non-trivial capsule support. To Lorenzo's question: testing from Apple client was all per-flow stuff, not interface level VPN, so we do have our "modern" socket replacement that has userspace IP stack, so they easily worked with that. For Apple platforms, for BSD sockets, we've had a way to bridge those for quite a while, as an Application-Level VPN, and we can divert sockets over there, so I don't know if Android has that, but we were able to test with an app that doesn't know anything about that and just opening sockets.
David: We had capsule interop with WebTransport, but having more is always better. For mapping to BSD APIs, the way I've been thinking about it: CONNECT-IP fits into a suite, if you look at BSD APIs or other more modern better APIs like what Apple has, means that app running on your phone can open UDP/TCP but not a raw socket. If the app wants to open a TCP socket, that gets mapped to connect. You can do that with a collaborating app where it calls your API, if you have kernel support then that works too: Android, pretty please? Linux?
Lorenzo, Alex: While internet was out in the room.
Lorenzo: Kernel support is a ways out. Primary case is tun case, well specified, one at a time where you get an IP address, and even for many apps that works fine, for v4 you're not as happy. Thanks, that does clarify.
Alex: This is for when you aren't happy with TCP/UDP from CONNECT/CONNECT-UDP

Eric Kinnear: Intarea review in parallel with WGLC in a few weeks after editorial pass.

## Individual Drafts (60 minutes)

### Proxying Listener UDP in HTTP - Abhijit Singh, 10 minutes

Lorenzo: Usually apps will bind to an IP address, so something to consider. Dual stack sockets are additional complexity.
Tommy: This is a primary use case for a MASQUE proxy and we've been running for a few years now and that really should be addressed.
Alessandro: This is useful and we should work on it.
David: Conceptually, CONNECT-UDP is a socket, and you do bind, and you get info on every incoming packet, and you can sendto. The more we think about this, more and more MASQUE feels like a NAT over HTTP. Irony. You could add an option to use masque and say "hey, here's the public address that we're using on the outside"
Lorenzo: You need to pass that through to get socket API mode.
David: I don't think you need that, you don't need it for NATs today.

Randell Jesup (via Jabber): +1 to useful.
Eric Orth (via Jabber): +1 as useful and appropriate to fit into a recharter of this WG. I have some ideas for further expansions/improvements of this draft, but nothing blocking that can't wait for adoption.

David: Adoption? 
Eric: Hearing quite a bit of interest, adoption is on the table if we decide to charter it in.

### QUIC-Aware Proxying Using HTTP - Eric Rosenberg, 15 minutes

Martin Duke: Thanks for this. Clarifying question, which quiche
Eric Rosenberg: Cloudflare quiche
Martin: Use case is clear, but having thought about this there are some footguns. VCID, wondering if correct to bind 1:1 to real CIDs. Trouble reasoning about the threat model. Do you want to change the CID that comes out from the proxy to the target, answer no? But may be wrong. Interesting property, if the proxy client connection consists of this, then you have basically no ACK-feedback on the MASQUE connection. Lots of CCs will kill cwnd if they don't get anything. Transport area may allow some better review.
Eric: I don't think they have to be 1:1, we still need to flesh out the details of the protocol, but the idea is that the VCID can change.

Mike Bishop: I think this is an important problem to solve, I think this draft is an elegant way of doing it, even if details to work out. Also, in the real world you're often not using this just for one connection that you set up the tunnel and never touch the control channel again, usually there are other connections that are coming and going, and we can do that post-adoption. I think this should be in scope for a recharter.

Alessandro Ghedini: I think there are clear benefits and this is something we should work on. For VCIDs, I think we would have the same problem between client and proxy and having to choose targets. I was wondering if existing mechanism would also solve that problem.
Eric: I think we'd want to understand a little more, but the VCIDs are there in both directions.

Lucas Pardue: Clear and good use case, some details to hammer out, but something we should be working on. Later draft is much clearer now. I think the way we can handle control channels is good, we should do this work here. Let's come up with technical solutions for this.

Martin Thomson: The thing that bothers me about this design is that traffic analysis here is trivial and what this design needs is reencryption at the proxy in both directions. That's unfortunate and less efficient, but the privacy properties are not that great until you do that. Ekr had the same point previously.

David: Fun with mics. Agree with people that there is a need for this, it solves something, there are a lot of details to hammer out. +1 to rechartering for this to fit and then adopting this. Martin T. and Eric R. are operating with different threat models so we may need different modes of operation. Forwarding mode means that eBPF can forward on the NIC without ever touching the CPU, which is crazy awesome. That makes a huge difference and allows you to scale way more. We may need both modes. WG is the right place.

chair(Eric): This is the right place to talk about this.

### HTTP Access Service Description Objects - Ben Schwartz, 10 minutes

Eric Orth: I like this, I want to see it happen somewhere, but I'm not totally convinced it fits for a potential recharter of this group. Assuming we want this group to be focused on winding down after the necessary work. This proposal is more of a general proxying thing. We don't want to become a general proxying working group. Not sure we want this to become a permanent working group, so let's not recharter to allow this, but please make this happen somewhere.

Tommy Pauly: Thank you for updating this, this is better and more clear than the previous proposal. Still on the fence about the exact details here, what I imagine in the short term for how this configuration is going to work, I see two main paths for configuring: (1) like you're talking about, we have legacy proxy configuration boxes somewhere in OS or browser settings. The user just types in a name. CONNECT works already, for CONNECT-UDP/IP we defined a well-known URI so we could just try those and have a well-known location off of the name. That seems to work okay. (2) alternately, we have an application or MDM profiles with a more complex language for describing your URLs that could come in some other format. What you have here definitely enables more automatic use cases, but I'd want to see more discussion among this group or another group more broadly about what's the exact user flow and who is going to need this information that can't get it from a well-known URI and also cannot have a profile describe it in a richer way. 

Alex Chernyakhovsky: I think this is very cool and I think you're on to something here, but I'd like to understand what the user story is here, how are you envisioning people using this and what is the right design there. I'm not sure that we're the right group, agree with messages in Zulip to take this to DISPATCH. I can't remember the last time I used proxy UI to configure something other than in development.
Ben: We don't really use these UIs, they exist, but vastly more people install VPN apps from their app store than use IPsec configuration UIs that every OS has, I think that's a bad thing and we should have standardization.

Martin Thomson: I suggest a DISPATCH, there's a really difficult discussion around scope. We've mentioned DoH, OHAI, all sorts of things that a very concerned internet citizen might ask in the interest of users about centralization. 
Ben: I don't see a big centralization concern here, if anything it makes it easier to spin up new competing access services by lowering the barrier to new ones.
Martin: One UI affordance and one authentication, that's what bothered me.

David: I think there's something here, I want to see this work go further so we can better understand, but will +1 previous comments that MASQUE isn't the venue

Eric Kinnear: Looking like there's support for this work, but not necessarily in this group, let's suggest a DISPATCH.

### HTTP Proxy-Status Parameter for DNS Information - Tommy Pauly, 10 minutes

David: Thanks, I think this is useful. I think this space of "more information about the DNS thorugh an HTTP proxy" makes a lot of sense. I want to see things like this happen. In terms of where to do the work, my gut feeling is that this applies to more than just MASQUE and so slight thought that it would apply to httpbis.

Martin Thomson: This seems like a very narrow solution. We know that we have a more general problem sitting right there, I'd rather explore those other solutions before deciding to do the narrow thing. Clients often need more info about what was done. Maybe ADD instead.
Tommy: Sure, I definitely agree that we should do the bigger stuff too, I also think there is incremental value in doing the small thing, it's a useful hint. 

Ben Schwartz: I don't know that I believe in the CNAME cloaking use case really, but I do want this. To Lorenzo's point about supporting POSIX APIs, this is very useful if you're trying to emulate getaddrinfo which, at least optionally, does surface CNAME results. I see this as distinct from a deeper system for conveying DNS results generally. General problem runs into centralization issues. Please change name, "dns-used" is not good enough. Suggestion is "cname" or something similar.

Mirja Kühlewind: I think we should take DNS use cases as in-scope for rechartering. I think there is good work to be done here.

### A Sequence Number Extension for HTTP Datagrams - Marcus Ihlar, 5 minutes

Gorry Fairhurst: Clarification: will or might use MPQUIC? 
Marcus: Lots of discussion. There is agreement that it will be used, but not necessarily only solution, could be MPDCCP, not fully up to date, more clarity coming next week.

David: Thanks for presenting, not convinced that this is a good idea, concept itself about partial reordering on the proxy, smells like a TCP accelerator. Those are good for ossification and making performance worse, I have numbers for that. To the question of should this happen, I believe this should not happen in the 3GPP and we should not encourage them to run with scissors because that's how you can hurt yourself. This topic is in scope for MASQUE, but I'm not sure we should progress it because it might not be a good idea. I would be down to have that conversation in MASQUe. 

Hang Shi: I think it's a good idea to do reordering in the proxy for some weak endpoints, but lots of discussion about protocol ossification, I think we need to find a middle-ground for function between proxy and the endpoint.
Kazuho Oku: IIRC, it has been an intentional choice in the QUIC working group to not expose the sequence number, that was based on the idea that it will decrease the performance of a multistreaming protocol that doesn't have head of line blocking. That's a major concern that you could lose the benefits from those choices.

### HTTP Datagrams, UDP Proxying, and Extensible Prioritization - Lucas Pardue, 5 minutes

David: Thumbs up for masque, we punted to avoid delay but now that it's there we want it.
Ben: I don't get it, where's the queue, these are datagrams. Want priorities outside packets.

### Modernizing HTTP Forward Proxy Functionality - Ben Schwartz, 5 minutes

Tommy Pauly: I find the TCP case a bit more compelling. Could be done in HTTP and not in MASQUE. If we do this, we should be getting enough new functionality, for example, for TCP we find the ability to do \*.\* TCP listeners. That is compelling and something you couldn't do before. Split the TCP stuff.

# Chartering discussion

Lucas: Clarifying question: What do you mean by core documents? Stuff we defined only in MASQUE, or things we depend on from other groups. 
Eric: Our original three, HTTP datagrams, Connect-UDP, Connect-IP
Lucas: The priorities example is an extensions to a document that was written in the HTTP working group, but the extension affects the behavior of what we're doing here. Would that potentially be in scope too?
David: Yes, I think of Lucas's datagram priorities as an extension to HTTP datagrams, even if in the details you're right. I would phrase this in such a way that this is in scope. This new paragraph feels a bit too broad to me, our IESG overlords don't want us doing too much stuff. I think it would make more sense for us to focus on extensions to our core documents (some wording needed from the previous point). Those should be in scope. I really like the idea of real world use cases, but that's kind of ill-defined, so I'd like to see that tightened, but I really like that concept. Let's not go into hypotheticals, let's keep it real. Where I draw the line would be to say that if something applies to HTTP more generically, it shouldn't be in scope here, it should be in scope of HTTP. The remaining thing that is still here is discovery and configuration. I'm not sure that should be in scope, at least because so far all of the proposals that we've seen in this space are not specific to MASQUE and are encompassing HTTP and other things, perhaps OHAI. I would explicitly mark that as out of scope. Remove 3/4 of this paragraph. 
Eric: Previously discussed splitting discovery and configuration, was out of scope before. This proposal brings it in, do we want that or should we keep it out of scope? 

Eric Orth: Danger of being too broad is if we become general proxying working group, I think we really need to make sure that whatever we have written here is focused only on proxying for CONNECT-IP or CONNECT-UDP. Configuration, discovery, extensions, etc. are all good, but it needs to be about proxying UDP and IP. Leave out anything that's for general proxying. Anything in the general area means that this working group becomes permanent.

Tommy: Agree with previous, I think the text should be very specific and talk about enhancements to CONNECT-UDP and CONNECT-IP. Be specific about doing work that adds missing functionality that prevents use cases or improves performance. Not sure that we should have authorization, not sure what modification of proxied content means. Rather than discovery and configuration, how about coordination between resources, some of what Ben was getting at, recognizing that "this one server does CONNECT-IP and CONNECT-UDP and CONNECT" and there may be some use cases where we want to bundle them together and coordinate those different resources. I don't have text for that right now, but that might cover a more narrow sort. I think it's fine to say that we don't cover TCP stuff and that that goes to HTTP, but perhaps TCP listening (for example) is an example of a case where we should bring that to HTTP and ask if they want to take that on or if they want to punt it over to MASQUE, because that feels more like something for MASQUE.

Martin Duke: Definitely not proxying group. The current consensus is that we are also not going to be masquem. Scope to actual problems we're trying to solve, explicitly. Set of possible problems.

Alex: Interesting discussion in terms of overlap, not opposed to service discovery (for example DoH endpoint on your MASQUE proxy). How do we do layer breaking things that help us make a high performance useful service.

Martin Thomson: I think this needs an exclusion clause saying that we're not doing generic proxying.

David: Not quite as tight as explicitly listing the extensions, but tightly listed by use case.

Lorenzo: Trying to figure out what MASQUE is going to offer, can we proxy at fine grained IP level, and everything that is in pursuit of that goal. How can a client appear to be at the proxy and still have everything work as if it were directly connected to the internet.