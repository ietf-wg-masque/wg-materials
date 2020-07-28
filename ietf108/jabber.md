# MASQUE Jabber @ IETF 108

Times are in US-Pacific

3:53 AM Tobia Castaldi: Hi everyone
3:53 AM Tobia Castaldi: If you have to present please make sure you follow the instructions described here: https://www.ietf.org/media/documents/Documentation-Meetecho-IETF.pdf
4:02 AM Martin Thomson: Oh great, video broken again.  This is great stuff.
4:05 AM Mirja Kühlewind: Martin maybe you can give the meetecho team you IP address, so they can check your case specifically...?
4:06 AM Tommy Pauly: Yeah, but he's hiding his IP address by sending his connections through a MASQUE proxy...
4:06 AM alex-meetecho: Martin Thomson: details that go beyond "video is broken" won't be completely useless
4:06 AM Benjamin Schwartz: I can take some notes
4:06 AM Ted Hardie: If you need something relayed, please put MIC: here in the jabber room.
4:07 AM Martin Thomson: alex-meetecho: Where should I send a screenshot?
4:09 AM Martin Thomson: alex-meetecho: the recvonly video flow is connected and receiving bytes, but nothing displays
4:09 AM Meetecho: Martin Thomson: you can send it to ietf￼.com thx, even though screenshots would only partly help. We'd need some more details like browser, possibly webrtc stats if you can gather them, etc.
4:09 AM Meetecho: Martin Thomson: have you tried hovering on that video and hitting the pause button to stop it, and the play button to open it again?
4:09 AM Meetecho: What's the bitrate you see for that video?
4:09 AM Brian: i'm looking forward to fully-fledged protocol features that are accountable to lack of caffeine, actually...
4:10 AM Meetecho: If it's much less than, let's say, 200/300kbps it may be an issue another IETFer experienced
4:11 AM Martin Thomson: 600kbps seems fine
4:12 AM Martin Thomson: no connectivity issues of note: srvflx candidates connected happily
4:12 AM Meetecho: Ok so the browser is receiving the data but is either unable to decode it (missing keyframes?) or is not rendering it: have you checked if the HTML5 video element is playing? (right-clicking + show controls should confirm it)
4:15 AM Martin Thomson: It showed the "Play" option, which I clicked to no effect.
4:16 AM Meetecho: The pause/play trick in the controls in the upper/left part of the video didn't help either? Otherwise a page refresh may fix it
4:16 AM Mike Bishop: FWIW, on my (working) session, the same thing gives me a pause button.
4:18 AM Martin Thomson: I had the same problem yesterday, and the first session eventually started working with no intervention from me.
4:18 AM Martin Thomson: but I went through an entire session later with no video
4:21 AM spencerdawkins: We really need to pick a place to talk about nested congestion control - I'm guessing ICCRG? But that is a topic that's come up in multiple places.
4:21 AM Martin Duke: "Considerations" for that are in the charter'
4:22 AM Ted Hardie: If you want something relayed to the queue, pleae put MIC:  in the line you'd like relayed.
4:22 AM Martin Thomson: Not sure that I agree with Mike here, because streams provide reliability.
4:23 AM Alan Frindell: not ready for unreliable qpack?
4:23 AM Martin Thomson: hah
4:23 AM Ted Hardie: @Alan We can call it unpack!
4:24 AM spencerdawkins: @Martin Duke - is nested congestion control close enough to engineering for the IETF? :-)￼
I was thinking that it still involved research, fairly recently.
4:24 AM Martin Duke: New algorithms are out of scope, but "recommendations" are in scope. The WG should write down something about this to avoid dumb stuff
4:25 AM Ted Hardie: I think the friction for supportng new connect methods is actually pretty high, even if we have a few at the moment.
4:25 AM Ted Hardie: So Methods make sense to me.
4:25 AM spencerdawkins: @Martin Duke - I'll probably say that in LOOPS on Friday :D￼
4:26 AM Martin Thomson: wow, video started.  All it took was for me to look at another tab for long enough, it seems
4:27 AM Mirja Kühlewind: @Spencer: there is also https://datatracker.ietf.org/doc/draft-pauly-tsvwg-tcp-encapsulation/
4:27 AM Daniel Havey: The Meetecho feature that shows the name is working now. But please say your name anyways.
4:28 AM ekinnear: We got a good bit of feedback and discussion on that draft in tsvwg, so not saying that’s the home for it, but at least we found _some_ of the right people :)￼
4:28 AM Mike Bishop: @MT, that's true, and would require retransmission logic for H3 frames.  But there are several frame types where you don't need to care about ordering.  I'm not necessarily saying we do it, but we've discussed it, so there may be value in not closing the door on them.
4:29 AM spencerdawkins: @Mirja - right. Tommy presented that in TSVAREA a while back, but the conversation then was about IKEv2 over TCP, and it wasn't obvious to me that it generalized. Thanks for the clue.
4:32 AM Martin Thomson: So WebTransport seems like a strict superset of this in terms of functionality.
4:33 AM Martin Thomson: Mike Bishop: we talked about having stream-per-message for some of this so that we could get the reliability.  That was considered distasteful.
4:33 AM achernya: "this" being masque, or connect-udp, or the pattern slide?
4:33 AM Mirja Kühlewind: +1 to Magnus, I think it would be nice to work on UDP proxying and IP proxying and see how similar they are
4:34 AM Victor Vasiliev: FWIW, the conceptual way I think about WebTransport over HTTP is "HTTP resource with TE that makes payload look like a QUIC connection", so it makes sense for it to feel like a superset
4:35 AM nygren : Will put here
4:35 AM spencerdawkins:  New rule. At least one chair must be in a timezone where they're likely to be awake at any IETF virtual meeting ...
4:35 AM Christopher Wood: Blame Meetecho.
4:35 AM ekinnear: Heh I like this plan
4:35 AM lpardue: what if I'm a nightowl?
4:36 AM Meetecho: Christopher Wood: ?
4:36 AM Martin Thomson: I like this, but I'm not sure about the proxy handling either.  David will recall a few issues being raised.
4:36 AM Ted Hardie: @spencer Since we can't travel, that could mean we need more chairs.
4:36 AM Mirja Kühlewind: I also had a audio error in another session. looks that this as well.
4:36 AM lpardue: +1 EKR
4:36 AM nygren : Another reason a CONNECTX might make sense would be for cross-protocol interactions.  In-particular, for handling ICMP(v6) responses to UDP messages such as for PMTUD/PTB.  Although the proxy may/will need to translate those regardless so it may be that that's a special-case and just having a general mechanism for relaying those back across the various CONNECT-* methods would also be fine.
4:36 AM Alan Frindell: +1
4:36 AM Mirja Kühlewind: meetecho Nygren was trying to join the queue but then got kick of audio immediately
4:37 AM Martin Thomson: The problem is that one proxy will pass an unknown method...
4:37 AM Ted Hardie: @nygren I've enqueued to relay that.
4:37 AM spencerdawkins: @lpardue - all you have to do is be awake. @Ted, Lucas may have another solution :D￼
4:37 AM Martin Thomson: Connection doesn't work in h2
4:37 AM Meetecho: Mirja Kühlewind: that can happen if permission to access the device was rejected by mistake (which Chrome remembers, for instance). In that case, permissions should be reset in the settings
4:37 AM Eric Rescorla: I'm not there, but I would need to think about it in any case
4:38 AM Magnus Westerlund too: Chairs: Time check
4:38 AM Alan Frindell: Doesn't it have to be hop-by-hop?  Otherwise you could end up with collisions?


4:38 AM Eric Rescorla: Is Brian in disguise?
4:38 AM ekinnear: @Magnus ack
4:38 AM Mirja Kühlewind: meetecho I believe I had that problem when I only allowed for the mic but not the camera...
4:39 AM Martin Thomson: It's odd to see Brian during the daytime.
4:39 AM Christopher Wood: Cutting the queue here.
4:39 AM Mirja Kühlewind: @ekr super hot here today. I didn't even dare to sit outside...
4:39 AM lpardue: it might be hot in Zurich but Brian looks cool
4:41 AM Mike Bishop: I think it's workable with or without an explicit proxy list -- the client can ask the (first) proxy to connect to the second proxy, or can request the end point and the proxy knows that's how it gets a UDP connection.
4:42 AM spencerdawkins: @Mike - +1
4:42 AM nygren : I think ￼ is right — it looks like the browser I was using (Firefox) didn't have mic/video permissions in Mac OS X but wasn't usefully telling me this.


4:43 AM Martin Thomson: So you can use this without an on-connection indication of support, but you degrade to a single-stream/single-connection in-ordered delivery design.  That's not bad, but you miss the upgrade to DATAGRAM.
4:43 AM spencerdawkins: +1 EKR - we've gotta stop calling everything a proxy no matter what it does. We talked about this at charter time.
4:43 AM Meetecho: On MacOS you may have to also allow the browser to access those devices in the first place, for browsers that aren't safari: it's needed for screensharing in Catalina, for instance, but some other people needed to do the same for mic and webcam too (otherwise they're automatically rejected). The setting is usually found in the Privacy section of the OS
4:44 AM achernya: I suppose we're also confusing things by using a CONNECT-style method which evokes forward proxy but the diagram is showing a reverse roxy
4:44 AM Tommy Pauly: MASQUE should be forward proxies in general
4:45 AM Tommy Pauly: I think the diagram is confusing things =)￼
4:45 AM spencerdawkins: "protocol design is hard".
4:45 AM Martin Thomson: A setting to enable CONNECT-UDP seems like it is a reasonable strategy.  Then there is no confusion about what is going on.  It's very tricky if you allow DATAGRAM and CONNECT-UDP to be orthogonal negotiations.
4:45 AM achernya: MASQUE is, but the implementation it runs over may involve reverse proxies :)￼
4:45 AM Tom Jones: to left tab will a little plot
4:45 AM Mirja Kühlewind: but there is also the TOR like use case where you maybe want to talk to multiple (forward) proxies
4:45 AM Tom Jones: top*
4:45 AM Martin Thomson: what was that for?
4:46 AM Martin Thomson: Oh, I would have hummed for "adopt", but I thought that it was a mistake.
4:46 AM Martin Thomson: We need a better voting tool.
4:46 AM Mirja Kühlewind: maybe we should redo the first hum
4:46 AM Eric Rescorla: well, who cares
4:46 AM Eric Rescorla: because we have a hum for adoption anyway
4:47 AM spencerdawkins: I could have also gone for "adopt the next revision". It's really close, if it's not ready.
4:47 AM Martin Thomson: Yeah, what I want to know is who hummed against and why.
4:47 AM Eric Rescorla: VOTING!
4:47 AM Mirja Kühlewind: actually on the mailing list we also "vote" anyway...
4:47 AM Erik Nygren: I didn't hum against, but concern would be if we want to take a different approach to better align with webtrans.
4:48 AM Martin Thomson: Vote early, vote often.
4:48 AM Eric Rescorla: Is he meaning to not show video?
4:48 AM lpardue: if we don't adopt the document and have no other proposal, then MASQUE is kinda a damp squib right?
4:48 AM Christopher Wood: Yeah, the hum was just to get a sense of direction in the room. We'll confirm and discuss reasons for and against adoption on the list.
4:49 AM Christopher Wood: (Responding to Mirja)
4:49 AM Martin Thomson: Erik, I share those concerns, but I have some amount of confidence that we can hold this until we're sure about the interplay between the two
4:49 AM ekinnear: @Erik Ack, I suspect we’ll have frequent communication with webtrans (especially as David is one of the webtrans chairs)
4:49 AM Ian Swett: +1 to Erik and MT's comments
4:50 AM Ian Swett: I assume "Indistinguishability" doesn't include traffic analysis?
4:50 AM Martin Thomson: Is the idea that you might use EAP over QUIC?
4:50 AM Christopher Wood: I assume that as well, Ian.
4:51 AM Eric Rescorla: yes
4:51 AM achernya: I'm not personally fond of EAP itself, but a pluggable mechanism for mutual or client auth should exist
4:51 AM Martin Thomson: Yeah, I don't think that indistinguishability is practically achievable.
4:51 AM dschinazi@jab.im: @MT yes I think a requirement is for this to be extensible enough to allow building EAP over it as an extension
4:51 AM dschinazi@jab.im: And it depends on the definition of Indistinguishability
4:54 AM achernya: 100% indistinguishable is an open research question, as there's work in identifying traffic through e.g., just length analysis
4:54 AM achernya: but I think we can do pretty well nonetheless
4:56 AM Martin Thomson: The flow control thing is weird.  That is something QUIC already does.
4:56 AM achernya: Datagram frames (IIRC) are not flow controlled, but streams are
4:56 AM Martin Thomson: Yes, that is right.
4:56 AM Martin Thomson: Did you want flow control for DATAGRAM?
4:56 AM achernya: We saw performance problems with QBONE until we turned QUIC flow control off entirely
4:57 AM Victor Vasiliev: Do you mean congestion control?
4:57 AM Martin Thomson: Sure, but that requires no protocol changes, just send 2^62-1 as your MAX_STREAM_DATA/MAX_DATA.
4:57 AM achernya: VictorL No, I mean both
4:57 AM Martin Thomson: Disabling congestion control seems reasonable, assuming that you still employ it end-to-end.
4:57 AM spencerdawkins: @Martin Thompson - I think it's weird, too. It seems like we're coming up with a lot of ways that allow you to do the same thing. Searching for a superset?
4:57 AM achernya: Martin: I think it's still worth documenting the expectation to be able to do so
4:58 AM Martin Thomson: achernya: it is usual practice to list that differently than your unsatisfied requirements
4:59 AM Martin Thomson: I doubt that you can disable congestion control if you have to use HTTP/2
4:59 AM achernya: Martin: Apologies, as the datagram stuff is still a draft we wanted to call it out as a requirement
4:59 AM Eric Rescorla: It seems like if this is out of scope than the routing thing ought to be as well
5:00 AM achernya: ekr: you can do NAT with routing as a primitive
5:00 AM spencerdawkins: #Martin Thomson - right - you can TRY to disable congestion control for HTTP/2, but that should be a NOOP.
5:00 AM achernya: you can't do the converse
5:00 AM Eric Rescorla: achernya: I'm talking about "Addressing Architecture"
5:00 AM Martin Thomson: Agree with Ekr here regarding routing.  If the proxy is acting as a router, then it needs to be a router.  If it is a remote IP endpoint, then it isn't a router.
5:00 AM Ted Hardie: So this is a "success" if it proxies the packets over to a network, which then null routes them?
5:00 AM Eric Rescorla: Ted Hardie: my ISP would tell you that
5:00 AM achernya: ekr: I don't understand your point. We're trying to say in scope: some way to convey IP addresses/routes. Out of scope: deciding what those IPs are
5:01 AM Eric Rescorla: achernya: perhaps this is not the best venue for this
5:01 AM spencerdawkins: I would say "thank you for doing this work" at the mike if we had more time ...
5:02 AM Erik Nygren: Consideration here might be how to handle IPv6+IPv4 dual-stack environments, as well as transitions between IPv4 endpoints on side and IPv6 endpoints on the other side.
5:02 AM lpardue: I think indistinguishability should be a non-requirement. If its good enough (ignoring traffic pattern analysis) to just "hide" things in QUIC packets then that is the default behaviour, and you'd have to work hard to make the packet contents distinguishable
5:02 AM Ted Hardie: @Erik did you want that relayed?
5:02 AM Erik Nygren: Probably no need to relay at the moment.
5:02 AM Martin Thomson: spencerdawkins: consider this a reminder that maybe the "thank you" was always unnecessary.  I know it's good to provide positive feedback, but a mailing list is a good way to do that.
5:02 AM ekr@jabber.org: I think I agree with Ben that it would be helpful to go back and ask what the requirements are for other IP tunneling thngs
5:03 AM ekr@jabber.org: So perhaps we need not reinvent

----

5:06 AM achernya: lpardue: That's not sufficient, because e.g., an ALPN would leak that you're doing ip proxying
5:06 AM lpardue: the TLS handshake is not HTTP
5:07 AM dschinazi@jab.im: @Lucas the Indistinguishability requirement is met by using h3
5:08 AM ekr@jabber.org: Well, if we're talking Tor, there is a bunch of prior art that uses fancy crypto, that this does not look like
5:08 AM Martin Thomson: what does this do for PMTUD?
5:09 AM Mirja Kühlewind: I think the whole idea is to hide IP address from the server without all the fancy crypto
5:09 AM Martin Thomson: This is not sufficient, sadly.  You need NEW_CONNECTION_ID at the level of the tunnel.
5:10 AM Magnus Westerlund too: You end up with two different, one for the packets being encapsualted and one for packets just forwarded with IP + UDP address+port rewriting
5:10 AM Mirja Kühlewind: yes you need a tunnel as well but then you don't need to all payload in the tunnel
5:10 AM Mirja Kühlewind: to send all payloads in the tunnel I mean
5:11 AM ekr@jabber.org: I'm having a lot of trouble following this, but if you want to have Tor-like properties, you need nested encryption
5:11 AM Martin Thomson: There is no draft for this presentation, right?
5:11 AM Mirja Kühlewind: no draft yet
5:11 AM Martin Thomson: Because this design doesn't work.
5:11 AM ekr@jabber.org: (though Tor uses tricks to avoid packet expansion)
5:11 AM Mirja Kühlewind: only some of the tor properties, that's why it's only tor-like
5:11 AM Martin Thomson: It would have been really good to get a draft so that we can discuss the design requirements.
5:11 AM ekr@jabber.org: Well, I have no idea what "tor-like" means
5:12 AM ekr@jabber.org: The place to start is with what the security objectives are
5:12 AM Martin Thomson: I can wait
5:13 AM Mirja Kühlewind: the main requirement is that no proxy on the path can see both the source IP address and the destination IP address
5:13 AM ekr@jabber.org: but if the connection IDs are carried in the clear, then it doesn't help
5:15 AM Martin Thomson: I don't see much point in continuing a discussion about something that we can't see or discuss before the meeting.
5:18 AM ekr@jabber.org: Yeah, I think this is sufficiently intricate that its really hard to make sense of this way
5:18 AM Martin Thomson: There is a reasonable design to achieve this outcome, but it requires that you
5:19 AM Martin Thomson: have a whole new negotiation protocol.
5:19 AM Mirja Kühlewind: why?
5:19 AM Martin Thomson: because otherwise you have linkability for packets across the proxy, or an unmanageable proxy
5:19 AM Martin Thomson: the proxy has to be able to select the connection IDs that are included in packets that are sent to it
5:20 AM Mirja Kühlewind: or actually not sure what you mean by "a whole new"
5:20 AM Mirja Kühlewind: are we design a negotiation protocol here?
5:20 AM Martin Thomson: 5-tuple routing works, but it doesn't scale very well
5:20 AM Martin Thomson: it's another layer of negotiation in addition to the handshake stuff described for CONNECT-UDP
5:21 AM Martin Thomson: CONNECT (TCP) is not low overhead
5:25 AM Mirja Kühlewind: Ben with SOCKS you can send arbitrary cleartext but with this you have to use QUIC as it depends on the connection ID which is encrypted
5:25 AM Mirja Kühlewind: I mean quic is encrypted
5:25 AM Mirja Kühlewind: not the connID
5:26 AM Benjamin Schwartz: (Focused on note-taking right now.)
5:29 AM Martin Thomson: flow label is not addressed, I would assume
5:30 AM Martin Thomson: As in, we might fix it per-"masque flow", which might do
5:30 AM Martin Thomson: but we need to say something or it could be linkable

----

5:31 AM Martin Thomson: ECN seems like a good extension
5:32 AM Chris Box: Re the skipped DSCPs: if we consider that Meetecho may eventually be streamed through MASQUE proxies I can see it would be very useful to have some kind of DSCP support through this chain, to allow suitable prioritisation of time-sensitive data.
5:32 AM Martin Thomson: Chris: this is ripe for extensions; think of adding multiple datagram-flow-id values for different DSCP codepoints
5:33 AM Tom Jones: why stop at 64k?
5:33 AM achernya: Probably because that's the maximum TLS record size, iirc
5:33 AM Martin Thomson: I assume that this has to fit in a UDP datagram
5:33 AM ekr@jabber.org: Max TLS record size is 2^14-1
5:33 AM Martin Thomson: Max TLS record size is 2^14
5:33 AM Martin Thomson: haha
5:33 AM Tom Jones: tls record size would have been a great argument to get rid of jumbograms
5:33 AM achernya: a bit smaller than I remembered, thanks
5:34 AM Nick Harper: TLS records aren't used in QUIC
5:34 AM ekr@jabber.org: well that too, but it won't stop MT and I from correcting people :)￼
5:34 AM Martin Thomson: again, extensions seem appropriate
5:37 AM Mirja Kühlewind: I would at least like to see native support for ECN :-)￼
5:37 AM ekr@jabber.org: Maybe we should have some extensions
5:37 AM dschinazi@jab.im: I think ECN should be the first extension
5:37 AM Ian Swett: You can proxy ECN back to the other endpoint, that seems sufficient for v1.
5:38 AM Benjamin Schwartz: Proxy ECN raises privacy concerns
5:38 AM Ian Swett: (nevermind, that's connect-IP, not connect-UDP)
5:38 AM Benjamin Schwartz: Oh, ok
5:40 AM Mirja Kühlewind: I actually just realised that we might have different few on if there is (another) IP header in the tunnel in the case of IP proxying
5:40 AM Jonathan Lennox: If you're doing ECN in a UDP application, you need to use the RECVTOS sockopt or the like.
5:40 AM achernya: IP, or UDP?
5:40 AM Mirja Kühlewind: for ups connect we only have quic over quic over udp over ip
5:40 AM Jonathan Lennox: UDP - the OS doesn't do any ECN support for UDP.
5:41 AM Mirja Kühlewind: so I would assume ip proxying would be .e.g quic over udp over quic over udo over ip
5:41 AM achernya: We've had discussion about CONNECT-UDP, requirements for CONNECT-IP, and a high-level design for CONNECT-QUIC
5:41 AM achernya: Only CONNECT-IP has inner IP headers here right now
5:41 AM Tom Jones: tos field support is patchy across operating systems
5:41 AM Tom Jones: linux and bsd support it, macos and windows don't (or didnt')
5:41 AM spencerdawkins:   If masque doesn't support a lot of this functionality as baseline, most of the extensions may not be useful in actual deployments ...
5:41 AM Mirja Kühlewind: I was assuming that you don't have an inner IP header but you are adding one before you send it to the target
5:42 AM Martin Thomson: spencerdawkins: let the market decide
5:42 AM achernya: That's true for CONNECT-UDP and CONNECT-QUIC, but not CONNECT-IP
5:43 AM Martin Thomson: set DF always as a requirement of the protocol
5:43 AM spencerdawkins: @Martin Thomson: - I believe you, but I expect the result to be mostly unusable in general deployments
5:43 AM achernya: +1 to tunneling the entire IP packet and not using CONNECT-UDP if you have these requirem,ents
5:43 AM Jonathan Lennox: macOS has RECVTOS
5:44 AM Jonathan Lennox: I think everything modern but Windows has it



## Raw Jabber Log

3:53 AM Tobia Castaldi: Hi everyone
3:53 AM Tobia Castaldi: If you have to present please make sure you follow the instructions described here: https://www.ietf.org/media/documents/Documentation-Meetecho-IETF.pdf
*Marcus Ihlar has joined the conversation.*
*Spencer Dawkins has joined the conversation.*
*Zhuangyan has joined the conversation.*
*Nick Harper has joined the conversation.*
*Cullen Jennings_256 has joined the conversation.*
*Jordi Paillissé Vilanova has joined the conversation.*
*Gorry Fairhurst has joined the conversation.*
*Chris Box has joined the conversation.*
*Karl Kathuria has joined the conversation.*
*Chi-Jiun Su has joined the conversation.*
*lpardue has joined the conversation.*
*Jingwang Ma has joined the conversation.*
*Meetecho has joined the conversation.*
*Alex Chernyakhovsky has joined the conversation.*
*Lucas Pardue has joined the conversation.*
*Tommy Pauly has joined the conversation.*
*Dan Druta has joined the conversation.*
*Christopher Wood has joined the conversation.*
*Alissa Cooper has joined the conversation.*
*Maksim Proshin has joined the conversation.*
*Dallas McCall has joined the conversation.*
*Carrick has joined the conversation.*
*Philip Eardley has joined the conversation.*
*Mirja Kühlewind has joined the conversation.*
*Ted Hardie has joined the conversation.*
*Alessandro Ghedini has joined the conversation.*
*Magnus Westerlund has joined the conversation.*
*Magnus Westerlund too has joined the conversation.*
*Alan Frindell has joined the conversation.*
*Nick Banks has joined the conversation.*
*Benjamin Schwartz has joined the conversation.*
*Karl Kathuria_268 has joined the conversation.*
*spencerdawkins has joined the conversation.*
*Marten Seemann has joined the conversation.*
*Stephan Wenger has joined the conversation.*
*Eckard Bogenfeld has joined the conversation.*
*Markus Amend has joined the conversation.*
*Alister Winfield has joined the conversation.*
*Akbar Rahman has joined the conversation.*
*Tobia Castaldi has joined the conversation.*
*Sean Turner has joined the conversation.*
*Jiao Kang has joined the conversation.*
*Hirochika Asai has joined the conversation.*
*Xavier de Foy has joined the conversation.*
*Eric Kinnear has joined the conversation.*
*Alexandre Gouaillard has joined the conversation.*
*alex-meetecho has joined the conversation.*
*Haizhou Du has joined the conversation.*
*Cullen Jennings has joined the conversation.*
*ekinnear has joined the conversation.*
*Murray Kucherawy has joined the conversation.*
*Alister Winfield_917 has joined the conversation.*
*Matthew Miller has joined the conversation.*
*Maxime Piraux has joined the conversation.*
*Renjie Tang has joined the conversation.*
*Eric Rescorla has joined the conversation.*
*Maxime Piraux has left the conversation.*
*Maxime Piraux has joined the conversation.*
*Philipp Tiesel has joined the conversation.*
*John Border has joined the conversation.*
*Greg White has joined the conversation.*
*Martin Thomson has joined the conversation.*
*Jari Arkko has joined the conversation.*
*Kazuho Oku has joined the conversation.*
*David Oliver has joined the conversation.*
*Chris Wendt has joined the conversation.*
*Mike Bishop has joined the conversation.*
*David Schinazi has joined the conversation.*
*Kirsty P has joined the conversation.*
*Brian Trammell has joined the conversation.*
*Martin Duke has joined the conversation.*
*Jonathan Lennox has joined the conversation.*
*Tom Jones has joined the conversation.*
4:02 AM Martin Thomson: Oh great, video broken again.  This is great stuff.
*François Michel has joined the conversation.*
*Craig Taylor has joined the conversation.*
*Daniel Havey has joined the conversation.*
*Chris Wendt has left the conversation.*
*Chris Wendt has joined the conversation.*
*Nicolas Kuhn has joined the conversation.*
*Christopher Inacio has joined the conversation.*
*Jasdip Singh has joined the conversation.*
*Shogo Asaba has joined the conversation.*
*Kiran Makhijani has joined the conversation.*
*Jingwang Ma has left the conversation.*
*dschinazi@jab.im has joined the conversation.*
*Nicolas Kuhn has left the conversation.*
*Jiao Kang has left the conversation.*
*Brian has joined the conversation.*
*Shogo Asaba has left the conversation.*
*ihlar has joined the conversation.*
*Jiao Kang has joined the conversation.*
*Jingwang Ma has joined the conversation.*
4:05 AM Mirja Kühlewind: Martin maybe you can give the meetecho team you IP address, so they can check your case specifically...?
*Alissa Cooper has left the conversation.*
*Alissa Cooper has joined the conversation.*
*Maxime Piraux has left the conversation.*
*Nicolas Kuhn has joined the conversation.*
*James Gould has joined the conversation.*
*Martin Thomson has left the conversation.*
*Martin Thomson has joined the conversation.*
*Maxime Piraux has joined the conversation.*
4:06 AM Tommy Pauly: Yeah, but he's hiding his IP address by sending his connections through a MASQUE proxy...
*achernya has joined the conversation.*
*Chris Inacio has joined the conversation.*
4:06 AM alex-meetecho: Martin Thomson: details that go beyond "video is broken" won't be completely useless
4:06 AM Benjamin Schwartz: I can take some notes
4:06 AM Ted Hardie: If you need something relayed, please put MIC: here in the jabber room.
*Marten Seemann has left the conversation.*
*Marten Seemann has joined the conversation.*
*Nicolas Kuhn has left the conversation.*
*Martin Duke has left the conversation.*
*Nicolas Kuhn has joined the conversation.*
4:07 AM Martin Thomson: alex-meetecho: Where should I send a screenshot?
*Erik Nygren has joined the conversation.*
*Michael Scharf has joined the conversation.*
*Jari Arkko has left the conversation.*
*Simon Romano has joined the conversation.*
4:09 AM Martin Thomson: alex-meetecho: the recvonly video flow is connected and receiving bytes, but nothing displays
4:09 AM Meetecho: Martin Thomson: you can send it to ietf￼.com thx, even though screenshots would only partly help. We'd need some more details like browser, possibly webrtc stats if you can gather them, etc.
*Bernard Aboba has joined the conversation.*
*nygren  has joined the conversation.*
4:09 AM Meetecho: Martin Thomson: have you tried hovering on that video and hitting the pause button to stop it, and the play button to open it again?
*Craig Taylor has left the conversation.*
*Sean Turner has left the conversation.*
*Craig Taylor has joined the conversation.*
4:09 AM Meetecho: What's the bitrate you see for that video?
4:09 AM Brian: i'm looking forward to fully-fledged protocol features that are accountable to lack of caffeine, actually...
*Sean Turner has joined the conversation.*
4:10 AM Meetecho: If it's much less than, let's say, 200/300kbps it may be an issue another IETFer experienced
*Mo Zanaty has joined the conversation.*
*Gorry Fairhurst has left the conversation.*
*Martin Duke has joined the conversation.*
*Gorry Fairhurst has joined the conversation.*
4:11 AM Martin Thomson: 600kbps seems fine
*Simon Pietro Romano has joined the conversation.*
4:12 AM Martin Thomson: no connectivity issues of note: srvflx candidates connected happily
*Jingwang Ma has left the conversation.*
*Jingwang Ma has joined the conversation.*
*Luca Niccolini has joined the conversation.*
4:12 AM Meetecho: Ok so the browser is receiving the data but is either unable to decode it (missing keyframes?) or is not rendering it: have you checked if the HTML5 video element is playing? (right-clicking + show controls should confirm it)
*Jiao Kang has left the conversation.*
*Jiao Kang has joined the conversation.*
*Haizhou Du has left the conversation.*
*Joerg Ott has joined the conversation.*
*Haizhou Du has joined the conversation.*
4:15 AM Martin Thomson: It showed the "Play" option, which I clicked to no effect.
*Dragana Damjanovic has joined the conversation.*
*Florin has joined the conversation.*
4:16 AM Meetecho: The pause/play trick in the controls in the upper/left part of the video didn't help either? Otherwise a page refresh may fix it
4:16 AM Mike Bishop: FWIW, on my (working) session, the same thing gives me a pause button.
*David Oliver has left the conversation.*
*David Oliver has joined the conversation.*
*Hannu Flinck has joined the conversation.*
*Jiao Kang has left the conversation.*
*Jiao Kang has joined the conversation.*
4:18 AM Martin Thomson: I had the same problem yesterday, and the first session eventually started working with no intervention from me.
4:18 AM Martin Thomson: but I went through an entire session later with no video
*Craig Taylor has left the conversation.*
*Craig Taylor has joined the conversation.*
*Hirochika Asai has left the conversation.*
*Hirochika Asai has joined the conversation.*
*David Oliver has left the conversation.*
*David Oliver has joined the conversation.*
*Chathura Sarathchandra has joined the conversation.*
4:21 AM spencerdawkins: We really need to pick a place to talk about nested congestion control - I'm guessing ICCRG? But that is a topic that's come up in multiple places.
*Anna Brunstrom has joined the conversation.*
4:21 AM Martin Duke: "Considerations" for that are in the charter'
*Victor Vasiliev has joined the conversation.*
*Gorry Fairhurst has left the conversation.*
*Craig Taylor has left the conversation.*
*Gorry Fairhurst has joined the conversation.*
*Craig Taylor has joined the conversation.*
4:22 AM Ted Hardie: If you want something relayed to the queue, pleae put MIC:  in the line you'd like relayed.
4:22 AM Martin Thomson: Not sure that I agree with Mike here, because streams provide reliability.
*Michael Scharf has left the conversation.*
*Michael Scharf has joined the conversation.*
*Craig Taylor has left the conversation.*
*Craig Taylor has joined the conversation.*
*David Oliver has left the conversation.*
*David Oliver has joined the conversation.*
4:23 AM Alan Frindell: not ready for unreliable qpack?
4:23 AM Martin Thomson: hah
4:23 AM Ted Hardie: @Alan We can call it unpack!
*David Oliver has left the conversation.*
*David Oliver has joined the conversation.*
*Craig Taylor has left the conversation.*
*Craig Taylor has joined the conversation.*
4:24 AM spencerdawkins: @Martin Duke - is nested congestion control close enough to engineering for the IETF? :-)￼
I was thinking that it still involved research, fairly recently.
*Tao Lin has joined the conversation.*
4:24 AM Martin Duke: New algorithms are out of scope, but "recommendations" are in scope. The WG should write down something about this to avoid dumb stuff
4:25 AM Ted Hardie: I think the friction for supportng new connect methods is actually pretty high, even if we have a few at the moment.
4:25 AM Ted Hardie: So Methods make sense to me.
4:25 AM spencerdawkins: @Martin Duke - I'll probably say that in LOOPS on Friday :D￼
*Bhavit Shah has joined the conversation.*
4:26 AM Martin Thomson: wow, video started.  All it took was for me to look at another tab for long enough, it seems
*Martin Duke has left the conversation.*
4:27 AM Mirja Kühlewind: @Spencer: there is also https://datatracker.ietf.org/doc/draft-pauly-tsvwg-tcp-encapsulation/
*Bhavit Shah has left the conversation.*
*Bhavit Shah has joined the conversation.*
4:27 AM Daniel Havey: The Meetecho feature that shows the name is working now. But please say your name anyways.
4:28 AM ekinnear: We got a good bit of feedback and discussion on that draft in tsvwg, so not saying that’s the home for it, but at least we found _some_ of the right people :)￼
4:28 AM Mike Bishop: @MT, that's true, and would require retransmission logic for H3 frames.  But there are several frame types where you don't need to care about ordering.  I'm not necessarily saying we do it, but we've discussed it, so there may be value in not closing the door on them.
*Bhavit Shah has left the conversation.*
*Tao Lin has left the conversation.*
*Tao Lin has joined the conversation.*
4:29 AM spencerdawkins: @Mirja - right. Tommy presented that in TSVAREA a while back, but the conversation then was about IKEv2 over TCP, and it wasn't obvious to me that it generalized. Thanks for the clue.
*Dan Druta has left the conversation.*
*Dan Druta has joined the conversation.*
*Martin Duke has joined the conversation.*
*David Smith has joined the conversation.*
*Jean-Michel Combes has joined the conversation.*
*Jean-Michel Combes has left the conversation.*
*Bhavit Shah has joined the conversation.*
*Ian Swett has joined the conversation.*
*Bhavit Shah has left the conversation.*
4:32 AM Martin Thomson: So WebTransport seems like a strict superset of this in terms of functionality.
4:33 AM Martin Thomson: Mike Bishop: we talked about having stream-per-message for some of this so that we could get the reliability.  That was considered distasteful.
4:33 AM achernya: "this" being masque, or connect-udp, or the pattern slide?
4:33 AM Mirja Kühlewind: +1 to Magnus, I think it would be nice to work on UDP proxying and IP proxying and see how similar they are
*Joseph Salowey has joined the conversation.*
*Jingwang Ma has left the conversation.*
*Jingwang Ma has joined the conversation.*
4:34 AM Victor Vasiliev: FWIW, the conceptual way I think about WebTransport over HTTP is "HTTP resource with TE that makes payload look like a QUIC connection", so it makes sense for it to feel like a superset
4:35 AM nygren : Will put here
4:35 AM spencerdawkins:  New rule. At least one chair must be in a timezone where they're likely to be awake at any IETF virtual meeting ...
*dragana has joined the conversation.*
4:35 AM Christopher Wood: Blame Meetecho.
4:35 AM ekinnear: Heh I like this plan
*Craig Taylor has left the conversation.*
4:35 AM lpardue: what if I'm a nightowl?
*Craig Taylor has joined the conversation.*
4:36 AM Meetecho: Christopher Wood: ?
*Stephan Emile has joined the conversation.*
4:36 AM Martin Thomson: I like this, but I'm not sure about the proxy handling either.  David will recall a few issues being raised.
4:36 AM Ted Hardie: @spencer Since we can't travel, that could mean we need more chairs.
4:36 AM Mirja Kühlewind: I also had a audio error in another session. looks that this as well.
4:36 AM lpardue: +1 EKR
4:36 AM nygren : Another reason a CONNECTX might make sense would be for cross-protocol interactions.  In-particular, for handling ICMP(v6) responses to UDP messages such as for PMTUD/PTB.  Although the proxy may/will need to translate those regardless so it may be that that's a special-case and just having a general mechanism for relaying those back across the various CONNECT-* methods would also be fine.
4:36 AM Alan Frindell: +1
4:36 AM Mirja Kühlewind: meetecho Nygren was trying to join the queue but then got kick of audio immediately
*Bhavit Shah has joined the conversation.*
4:37 AM Martin Thomson: The problem is that one proxy will pass an unknown method...
*Bhavit Shah has left the conversation.*
*Jiao Kang has left the conversation.*
4:37 AM Ted Hardie: @nygren I've enqueued to relay that.
4:37 AM spencerdawkins: @lpardue - all you have to do is be awake. @Ted, Lucas may have another solution :D￼
*Jiao Kang has joined the conversation.*
4:37 AM Martin Thomson: Connection doesn't work in h2
*Craig Taylor has left the conversation.*
4:37 AM Meetecho: Mirja Kühlewind: that can happen if permission to access the device was rejected by mistake (which Chrome remembers, for instance). In that case, permissions should be reset in the settings
*Craig Taylor has joined the conversation.*
4:37 AM Eric Rescorla: I'm not there, but I would need to think about it in any case
4:38 AM Magnus Westerlund too: Chairs: Time check
4:38 AM Alan Frindell: Doesn't it have to be hop-by-hop?  Otherwise you could end up with collisions?


4:38 AM Eric Rescorla: Is Brian in disguise?
4:38 AM ekinnear: @Magnus ack
4:38 AM Mirja Kühlewind: meetecho I believe I had that problem when I only allowed for the mic but not the camera...
4:39 AM Martin Thomson: It's odd to see Brian during the daytime.
4:39 AM Christopher Wood: Cutting the queue here.
4:39 AM Mirja Kühlewind: @ekr super hot here today. I didn't even dare to sit outside...
*Chris Box has left the conversation.*
*Chris Box has joined the conversation.*
4:39 AM lpardue: it might be hot in Zurich but Brian looks cool
*Craig Taylor has left the conversation.*
*Craig Taylor has joined the conversation.*
*Erik Nygren has left the conversation.*
*Erik Nygren has joined the conversation.*
4:41 AM Mike Bishop: I think it's workable with or without an explicit proxy list -- the client can ask the (first) proxy to connect to the second proxy, or can request the end point and the proxy knows that's how it gets a UDP connection.
*chi.jiun.su has joined the conversation.*
4:42 AM spencerdawkins: @Mike - +1
4:42 AM nygren : I think ￼ is right — it looks like the browser I was using (Firefox) didn't have mic/video permissions in Mac OS X but wasn't usefully telling me this.


4:43 AM Martin Thomson: So you can use this without an on-connection indication of support, but you degrade to a single-stream/single-connection in-ordered delivery design.  That's not bad, but you miss the upgrade to DATAGRAM.
4:43 AM spencerdawkins: +1 EKR - we've gotta stop calling everything a proxy no matter what it does. We talked about this at charter time.
*Craig Taylor has left the conversation.*
*Craig Taylor has joined the conversation.*
4:43 AM Meetecho: On MacOS you may have to also allow the browser to access those devices in the first place, for browsers that aren't safari: it's needed for screensharing in Catalina, for instance, but some other people needed to do the same for mic and webcam too (otherwise they're automatically rejected). The setting is usually found in the Privacy section of the OS
4:44 AM achernya: I suppose we're also confusing things by using a CONNECT-style method which evokes forward proxy but the diagram is showing a reverse roxy
4:44 AM Tommy Pauly: MASQUE should be forward proxies in general
4:45 AM Tommy Pauly: I think the diagram is confusing things =)￼
4:45 AM spencerdawkins: "protocol design is hard".
4:45 AM Martin Thomson: A setting to enable CONNECT-UDP seems like it is a reasonable strategy.  Then there is no confusion about what is going on.  It's very tricky if you allow DATAGRAM and CONNECT-UDP to be orthogonal negotiations.
*Maksim Proshin has left the conversation.*
4:45 AM achernya: MASQUE is, but the implementation it runs over may involve reverse proxies :)￼
*Tao Lin has left the conversation.*
*Tao Lin has joined the conversation.*
4:45 AM Tom Jones: to left tab will a little plot
4:45 AM Mirja Kühlewind: but there is also the TOR like use case where you maybe want to talk to multiple (forward) proxies
4:45 AM Tom Jones: top*
4:45 AM Martin Thomson: what was that for?
*Sean Turner has left the conversation.*
*Sean Turner has joined the conversation.*
4:46 AM Martin Thomson: Oh, I would have hummed for "adopt", but I thought that it was a mistake.
4:46 AM Martin Thomson: We need a better voting tool.
4:46 AM Mirja Kühlewind: maybe we should redo the first hum
4:46 AM Eric Rescorla: well, who cares
*Haizhou Du has left the conversation.*
*Haizhou Du has joined the conversation.*
4:46 AM Eric Rescorla: because we have a hum for adoption anyway
4:47 AM spencerdawkins: I could have also gone for "adopt the next revision". It's really close, if it's not ready.
4:47 AM Martin Thomson: Yeah, what I want to know is who hummed against and why.
4:47 AM Eric Rescorla: VOTING!
*Yanyuan QIN has joined the conversation.*
4:47 AM Mirja Kühlewind: actually on the mailing list we also "vote" anyway...
4:47 AM Erik Nygren: I didn't hum against, but concern would be if we want to take a different approach to better align with webtrans.
4:48 AM Martin Thomson: Vote early, vote often.
4:48 AM Eric Rescorla: Is he meaning to not show video?
4:48 AM lpardue: if we don't adopt the document and have no other proposal, then MASQUE is kinda a damp squib right?
4:48 AM Christopher Wood: Yeah, the hum was just to get a sense of direction in the room. We'll confirm and discuss reasons for and against adoption on the list.
4:49 AM Christopher Wood: (Responding to Mirja)
4:49 AM Martin Thomson: Erik, I share those concerns, but I have some amount of confidence that we can hold this until we're sure about the interplay between the two
*Kirsty P has left the conversation.*
*Simon Vera-Schockner has joined the conversation.*
4:49 AM ekinnear: @Erik Ack, I suspect we’ll have frequent communication with webtrans (especially as David is one of the webtrans chairs)
*Kirsty P has joined the conversation.*
4:49 AM Ian Swett: +1 to Erik and MT's comments
4:50 AM Ian Swett: I assume "Indistinguishability" doesn't include traffic analysis?
4:50 AM Martin Thomson: Is the idea that you might use EAP over QUIC?
*Richard Barnes has joined the conversation.*
4:50 AM Christopher Wood: I assume that as well, Ian.
4:51 AM Eric Rescorla: yes
4:51 AM achernya: I'm not personally fond of EAP itself, but a pluggable mechanism for mutual or client auth should exist
4:51 AM Martin Thomson: Yeah, I don't think that indistinguishability is practically achievable.
4:51 AM dschinazi@jab.im: @MT yes I think a requirement is for this to be extensible enough to allow building EAP over it as an extension
4:51 AM dschinazi@jab.im: And it depends on the definition of Indistinguishability
*Sanjay Mishra has joined the conversation.*
4:54 AM achernya: 100% indistinguishable is an open research question, as there's work in identifying traffic through e.g., just length analysis
4:54 AM achernya: but I think we can do pretty well nonetheless
4:56 AM Martin Thomson: The flow control thing is weird.  That is something QUIC already does.
4:56 AM achernya: Datagram frames (IIRC) are not flow controlled, but streams are
4:56 AM Martin Thomson: Yes, that is right.
4:56 AM Martin Thomson: Did you want flow control for DATAGRAM?
4:56 AM achernya: We saw performance problems with QBONE until we turned QUIC flow control off entirely
4:57 AM Victor Vasiliev: Do you mean congestion control?
4:57 AM Martin Thomson: Sure, but that requires no protocol changes, just send 2^62-1 as your MAX_STREAM_DATA/MAX_DATA.
4:57 AM achernya: VictorL No, I mean both
4:57 AM Martin Thomson: Disabling congestion control seems reasonable, assuming that you still employ it end-to-end.
4:57 AM spencerdawkins: @Martin Thompson - I think it's weird, too. It seems like we're coming up with a lot of ways that allow you to do the same thing. Searching for a superset?
4:57 AM achernya: Martin: I think it's still worth documenting the expectation to be able to do so
*Carrick has left the conversation.*
*Carrick has joined the conversation.*
4:58 AM Martin Thomson: achernya: it is usual practice to list that differently than your unsatisfied requirements
4:59 AM Martin Thomson: I doubt that you can disable congestion control if you have to use HTTP/2
4:59 AM achernya: Martin: Apologies, as the datagram stuff is still a draft we wanted to call it out as a requirement
4:59 AM Eric Rescorla: It seems like if this is out of scope than the routing thing ought to be as well
5:00 AM achernya: ekr: you can do NAT with routing as a primitive
5:00 AM spencerdawkins: #Martin Thomson - right - you can TRY to disable congestion control for HTTP/2, but that should be a NOOP.
5:00 AM achernya: you can't do the converse
5:00 AM Eric Rescorla: achernya: I'm talking about "Addressing Architecture"
5:00 AM Martin Thomson: Agree with Ekr here regarding routing.  If the proxy is acting as a router, then it needs to be a router.  If it is a remote IP endpoint, then it isn't a router.
5:00 AM Ted Hardie: So this is a "success" if it proxies the packets over to a network, which then null routes them?
5:00 AM Eric Rescorla: Ted Hardie: my ISP would tell you that
5:00 AM achernya: ekr: I don't understand your point. We're trying to say in scope: some way to convey IP addresses/routes. Out of scope: deciding what those IPs are
5:01 AM Eric Rescorla: achernya: perhaps this is not the best venue for this
*ekr@jabber.org has joined the conversation.*
5:01 AM spencerdawkins: I would say "thank you for doing this work" at the mike if we had more time ...
*Hannu Flinck has left the conversation.*
*Alan Frindell has left the conversation.*
5:02 AM Erik Nygren: Consideration here might be how to handle IPv6+IPv4 dual-stack environments, as well as transitions between IPv4 endpoints on side and IPv6 endpoints on the other side.
5:02 AM lpardue: I think indistinguishability should be a non-requirement. If its good enough (ignoring traffic pattern analysis) to just "hide" things in QUIC packets then that is the default behaviour, and you'd have to work hard to make the packet contents distinguishable
5:02 AM Ted Hardie: @Erik did you want that relayed?
5:02 AM Erik Nygren: Probably no need to relay at the moment.
*Renjie Tang has left the conversation.*
5:02 AM Martin Thomson: spencerdawkins: consider this a reminder that maybe the "thank you" was always unnecessary.  I know it's good to provide positive feedback, but a mailing list is a good way to do that.
5:02 AM ekr@jabber.org: I think I agree with Ben that it would be helpful to go back and ask what the requirements are for other IP tunneling thngs
*Renjie Tang has joined the conversation.*
5:03 AM ekr@jabber.org: So perhaps we need not reinvent

----

*Haizhou Du has left the conversation.*
*Haizhou Du has joined the conversation.*
*Brian Trammell has left the conversation.*
*Leif Johansson has joined the conversation.*
*Jiao Kang has left the conversation.*
*Jiao Kang has joined the conversation.*
*Rick Taylor has joined the conversation.*
*Brian Trammell has joined the conversation.*
*Haizhou Du has left the conversation.*
*Haizhou Du has joined the conversation.*
5:06 AM achernya: lpardue: That's not sufficient, because e.g., an ALPN would leak that you're doing ip proxying
5:06 AM lpardue: the TLS handshake is not HTTP
5:07 AM dschinazi@jab.im: @Lucas the Indistinguishability requirement is met by using h3
*Leif Johansson has left the conversation.*
5:08 AM ekr@jabber.org: Well, if we're talking Tor, there is a bunch of prior art that uses fancy crypto, that this does not look like
*Alan Frindell has joined the conversation.*
5:08 AM Martin Thomson: what does this do for PMTUD?
5:09 AM Mirja Kühlewind: I think the whole idea is to hide IP address from the server without all the fancy crypto
*Chris Box has left the conversation.*
*Chris Box has joined the conversation.*
5:09 AM Martin Thomson: This is not sufficient, sadly.  You need NEW_CONNECTION_ID at the level of the tunnel.
5:10 AM Magnus Westerlund too: You end up with two different, one for the packets being encapsualted and one for packets just forwarded with IP + UDP address+port rewriting
5:10 AM Mirja Kühlewind: yes you need a tunnel as well but then you don't need to all payload in the tunnel
*Bhavit Shah has joined the conversation.*
5:10 AM Mirja Kühlewind: to send all payloads in the tunnel I mean
5:11 AM ekr@jabber.org: I'm having a lot of trouble following this, but if you want to have Tor-like properties, you need nested encryption
5:11 AM Martin Thomson: There is no draft for this presentation, right?
5:11 AM Mirja Kühlewind: no draft yet
5:11 AM Martin Thomson: Because this design doesn't work.
5:11 AM ekr@jabber.org: (though Tor uses tricks to avoid packet expansion)
5:11 AM Mirja Kühlewind: only some of the tor properties, that's why it's only tor-like
5:11 AM Martin Thomson: It would have been really good to get a draft so that we can discuss the design requirements.
5:11 AM ekr@jabber.org: Well, I have no idea what "tor-like" means
5:12 AM ekr@jabber.org: The place to start is with what the security objectives are
5:12 AM Martin Thomson: I can wait
5:13 AM Mirja Kühlewind: the main requirement is that no proxy on the path can see both the source IP address and the destination IP address
*Jiao Kang has left the conversation.*
*Jiao Kang has joined the conversation.*
5:13 AM ekr@jabber.org: but if the connection IDs are carried in the clear, then it doesn't help
5:15 AM Martin Thomson: I don't see much point in continuing a discussion about something that we can't see or discuss before the meeting.
5:18 AM ekr@jabber.org: Yeah, I think this is sufficiently intricate that its really hard to make sense of this way
5:18 AM Martin Thomson: There is a reasonable design to achieve this outcome, but it requires that you
5:19 AM Martin Thomson: have a whole new negotiation protocol.
5:19 AM Mirja Kühlewind: why?
5:19 AM Martin Thomson: because otherwise you have linkability for packets across the proxy, or an unmanageable proxy
5:19 AM Martin Thomson: the proxy has to be able to select the connection IDs that are included in packets that are sent to it
5:20 AM Mirja Kühlewind: or actually not sure what you mean by "a whole new"
5:20 AM Mirja Kühlewind: are we design a negotiation protocol here?
5:20 AM Martin Thomson: 5-tuple routing works, but it doesn't scale very well
5:20 AM Martin Thomson: it's another layer of negotiation in addition to the handshake stuff described for CONNECT-UDP
5:21 AM Martin Thomson: CONNECT (TCP) is not low overhead
*Zhuangyan has left the conversation.*
*Alister Winfield_917 has left the conversation.*
*Carrick has left the conversation.*
*Carrick has joined the conversation.*
5:25 AM Mirja Kühlewind: Ben with SOCKS you can send arbitrary cleartext but with this you have to use QUIC as it depends on the connection ID which is encrypted
5:25 AM Mirja Kühlewind: I mean quic is encrypted
5:25 AM Mirja Kühlewind: not the connID
5:26 AM Benjamin Schwartz: (Focused on note-taking right now.)
*James Gould has left the conversation.*
*David Smith has left the conversation.*
*Nicolas Kuhn has left the conversation.*
5:29 AM Martin Thomson: flow label is not addressed, I would assume
5:30 AM Martin Thomson: As in, we might fix it per-"masque flow", which might do
*Chris Wendt has left the conversation.*
*Chris Wendt has joined the conversation.*
5:30 AM Martin Thomson: but we need to say something or it could be linkable
*Chathura Sarathchandra has left the conversation.*

----

5:31 AM Martin Thomson: ECN seems like a good extension
*Marcus Ihlar has left the conversation.*
*Marcus Ihlar has joined the conversation.*
5:32 AM Chris Box: Re the skipped DSCPs: if we consider that Meetecho may eventually be streamed through MASQUE proxies I can see it would be very useful to have some kind of DSCP support through this chain, to allow suitable prioritisation of time-sensitive data.
*Ali Hussain has joined the conversation.*
*Marcus Ihlar has left the conversation.*
*Marcus Ihlar has joined the conversation.*
5:32 AM Martin Thomson: Chris: this is ripe for extensions; think of adding multiple datagram-flow-id values for different DSCP codepoints
*Marcus Ihlar has left the conversation.*
5:33 AM Tom Jones: why stop at 64k?
*Marcus Ihlar has joined the conversation.*
*Haizhou Du has left the conversation.*
*Haizhou Du has joined the conversation.*
5:33 AM achernya: Probably because that's the maximum TLS record size, iirc
5:33 AM Martin Thomson: I assume that this has to fit in a UDP datagram
5:33 AM ekr@jabber.org: Max TLS record size is 2^14-1
5:33 AM Martin Thomson: Max TLS record size is 2^14
5:33 AM Martin Thomson: haha
5:33 AM Tom Jones: tls record size would have been a great argument to get rid of jumbograms
5:33 AM achernya: a bit smaller than I remembered, thanks
*Chunshan Xiong has joined the conversation.*
5:34 AM Nick Harper: TLS records aren't used in QUIC
5:34 AM ekr@jabber.org: well that too, but it won't stop MT and I from correcting people :)￼
*Haizhou Du has left the conversation.*
5:34 AM Martin Thomson: again, extensions seem appropriate
*Haizhou Du has joined the conversation.*
*Tao Lin has left the conversation.*
*Haizhou Du has left the conversation.*
*Tao Lin has joined the conversation.*
*Martin Duke has left the conversation.*
*Jiao Kang has left the conversation.*
*Jingwang Ma has left the conversation.*
5:37 AM Mirja Kühlewind: I would at least like to see native support for ECN :-)￼
5:37 AM ekr@jabber.org: Maybe we should have some extensions
5:37 AM dschinazi@jab.im: I think ECN should be the first extension
5:37 AM Ian Swett: You can proxy ECN back to the other endpoint, that seems sufficient for v1.
5:38 AM Benjamin Schwartz: Proxy ECN raises privacy concerns
5:38 AM Ian Swett: (nevermind, that's connect-IP, not connect-UDP)
*Chris Wendt has left the conversation.*
*Chris Wendt has joined the conversation.*
5:38 AM Benjamin Schwartz: Oh, ok
*Bhavit Shah has left the conversation.*
*Tao Lin has left the conversation.*
*Tao Lin has joined the conversation.*
*Tao Lin has left the conversation.*
*Tao Lin has joined the conversation.*
*Tao Lin has left the conversation.*
*Tao Lin has joined the conversation.*
*Chris Wendt has left the conversation.*
*Tao Lin has left the conversation.*
5:40 AM Mirja Kühlewind: I actually just realised that we might have different few on if there is (another) IP header in the tunnel in the case of IP proxying
5:40 AM Jonathan Lennox: If you're doing ECN in a UDP application, you need to use the RECVTOS sockopt or the like.
5:40 AM achernya: IP, or UDP?
5:40 AM Mirja Kühlewind: for ups connect we only have quic over quic over udp over ip
5:40 AM Jonathan Lennox: UDP - the OS doesn't do any ECN support for UDP.
5:41 AM Mirja Kühlewind: so I would assume ip proxying would be .e.g quic over udp over quic over udo over ip
5:41 AM achernya: We've had discussion about CONNECT-UDP, requirements for CONNECT-IP, and a high-level design for CONNECT-QUIC
5:41 AM achernya: Only CONNECT-IP has inner IP headers here right now
5:41 AM Tom Jones: tos field support is patchy across operating systems
5:41 AM Tom Jones: linux and bsd support it, macos and windows don't (or didnt')
5:41 AM spencerdawkins:   If masque doesn't support a lot of this functionality as baseline, most of the extensions may not be useful in actual deployments ...
5:41 AM Mirja Kühlewind: I was assuming that you don't have an inner IP header but you are adding one before you send it to the target
5:42 AM Martin Thomson: spencerdawkins: let the market decide
5:42 AM achernya: That's true for CONNECT-UDP and CONNECT-QUIC, but not CONNECT-IP
5:43 AM Martin Thomson: set DF always as a requirement of the protocol
5:43 AM spencerdawkins: @Martin Thomson: - I believe you, but I expect the result to be mostly unusable in general deployments
*Ali Hussain has left the conversation.*
5:43 AM achernya: +1 to tunneling the entire IP packet and not using CONNECT-UDP if you have these requirem,ents
5:43 AM Jonathan Lennox: macOS has RECVTOS
*Simon Romano has left the conversation.*
*Nick Banks has left the conversation.*
*Ian Swett has left the conversation.*
*Philip Eardley has left the conversation.*
*Ted Hardie has left the conversation.*
5:44 AM Jonathan Lennox: I think everything modern but Windows has it