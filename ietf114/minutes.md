# Administrivia (5 minutes)
* Blue sheets / scribe selection / NOTE WELL
* Agenda revision

# CONNECT-IP (75 minutes)
## IP Proxying Support for HTTP (Tommy Pauly presenting)

* CONNECT-IP was at the hackathon. Implementations in various codebases but no cross-implementation interop yet.
  - [David Schinazi] We were close to interop. MASQUE part was easy, but kernel part was harder. Magnus did a good summary at the end of the hackathon. Interop will be reached in the coming weeks.
* [Issue #46](https://github.com/ietf-wg-masque/draft-ietf-masque-connect-ip/issues/46) -- added default URI template. Parity with CONNECT-UDP.
  - [Martin Thomson] Using `.well-known` again. How do you plan to configure these things? Making people use the full template configuration is a better choice.
  - [Tommy Pauly] We came to consensus on this with CONNECT-UDP. Do not see a reason to go against that precedent.
  - [David Schinazi] Chairs forgot to mention a small update. H3-DGRAM and CONNECT-UDP went through WGLC and IESG and are now in RFC Editor's queue. Consistency argument convinced me.
  - [Ben Schwartz] I reluctantly agreed to this in CONNECT-UDP, and reluctantly agree with it here. CONNECT-UDP has a target, but CONNECT-IP does not, which is slightly different. Produces weird effects -- target is optional?
  - [Tommy Pauly] The PR defines `*` as empty.
  - [Ben Schwartz] But then you're not following the engine rules. Things could be unspecified, so you'd get `//`. Ambiguity between star and empty string is messy.
  - [Martin Thomson] If you put slashes between the curly braces then this could be made to work
  - [Tommy Pauly] Either could be wildcard, which means that introduces ambiguity
  - [David Schinazi] I tried to determine by reading RFCs if `//` or `*` is allowed. `//` is allowed, but lots of software tries to normalize it. But `*` is fine.
  - [Tommy Pauly] We've agreed we don't love `.well-known`, but that's consistent. What Ben's brought up is a separate component about empty versus star is a separate issue. Maybe we should think about empty versus star?
  - [David Schinazi] Level 3 URI template can specify `?target` lets you specify an empty variable that saves bytes
  - [Tommy Pauly] So we can consider it being always star except you drop it if you don't want to spend the bytes?
* [Issue #61](https://github.com/ietf-wg-masque/draft-ietf-masque-connect-ip/issues/61) and [Issue #62](https://github.com/ietf-wg-masque/draft-ietf-masque-connect-ip/issues/62) - Datagram MTU
  - [Gorry Fairhurst] PMTU could change after a path chnage as we traverse different links. 
  - [David Schinazi] If you have E2E QUIC connection, and pad QUIC initials, you'll be fine. But dynamic PMTUD inside QUIC and QUIC MTU goes down, then you need to drop the CONNECT-IP stream. Probably need to add a sentence
  - [Tommy Pauly] What happens if your PMTU goes down and you're doing static MTU?
  - [Gorry Fairhust] QUIC assumptionusing the Initial size is OK. But maybe things tunneled over QUIC that's surprising?
  - [David Schinazi] You're right, but this is also true of ethernet.
  - [Ben Schwartz] So you must do PMTU, and if it goes below 1280 you can't do IPv6?
  - [Tommy Pauly] Yes. We probably need to say `you MUST close the stream`.
  - [David Schinazi] I'll make sure the text is in the PR.
  - [Mirja Kühlewind] I'd like to not have a MUST since we can failover to streams.
  - [Tommy Pauly] OK, but we probably don't want that as default behavior
  - [Ben Schwartz] you SHOULD do the PMTU, and if it falls below 1280 you are no longer in compliance with IPv6 and what you do with that is up to you.
  - [Tommy Pauly] We could make it non-normative, e.g., you may abort the stream, or tunnel the packets over streams rather than datagrams.
  - [Tommy Pauly] Is everyone OK saying you SHOULD do PMTUD and that you MAY close the stream?
  - [Mirja Kühlewind] I'm OK with it.
  - [Magnus Westerlund] I am worried that SHOULD leaves us with loopholes. Intermediaries aren't helped by padding the initials.
  - [David Schinazi] Proposal, if you are sending over datagram, and you detect PMTU goes below the limit, then you MUST close the stream.
* [Issue #64](https://github.com/ietf-wg-masque/draft-ietf-masque-connect-ip/issues/64) - ICMP for error handling
  - [Martin Duke] To clarify, this is the proxy should forward ICMP messages and the client MUST handle them?
  - [Tommy Pauly] Proxy can generate them if client sends something invalid.
  - [Martin Duke] Maybe we just need some wordsmithing
  - [Ben Schwartz] Does this mean I can run traceroute on an IP tunnel that says TCP?
  - [Tommy Pauly] We do not guarantee forwarding ICMP in that case.
  - [Ben Schwartz] Does this mean the client can receive ICMP from not the target?
  - [Tommy Pauly] I'd like to hear other's opinions.
  - [David Schinazi] I think we should add a note that the source address could come from somewhere else. In terms of blackholing, we should avoid blackholing and the ICMP RFCs say this already. We don't need to say it again. Also you don't need ICMP to do traceroute. TCP can do it too
  - [Magnus Westerlund] Goal was to ensure that first, the endpoint can send ICMP back to client if something bad is sent, second to get feedback from beyond the proxy for port unreachable or something like that.
  - [Mirja Kühlewind] That's the same. If you get ICMP you don't know if it was received or if it was generated and sent to the client. If it comes from the outside, you don't know.
  - [Tommy Pauly] The addresses on the ICMP would be different, if it comes from the proxy IP address. 
  - [Alex Chernyakhovsky] If you have a restriction that says you can only do a particular protocol, you need to be able to restrict ICMP and other errors to only the ones that are in response to a particular thing that came from this tunnel.
  - [Tommy Pauly] Proxy should have the choice to only forward those, or if you have full control over the IP address it can forward everything.
  - [Alex Chernyakhovsky] At minimum, always get ICMP on point to point link, regardless of IPPROTO. If you want to guarantee that you get everything, you have to request ICMP specifically. However, proxy MAY be aware of the 5-tuples and forward it for you. 
  - [Antony Antony] [RFC 4301](https://datatracker.ietf.org/doc/html/rfc4301) is useful here.
* [Issue #66](https://github.com/ietf-wg-masque/draft-ietf-masque-connect-ip/issues/66)
  - [David Schinazi] This was a hard one for us to reason about, and a really hard one to explain. If you're wondering what we're talking about, that's normal. All of the editors thought about it and agreed this was a simple solution that avoided all the caveats.
  - [Martin Thomson] Does the PR change the wire format?
  - [Tommy Pauly] Interior of the capsule is now repeatable. 
  - [Magnus Westerlund] We need to be explicit if partial fulfillment is OK
  - [Tommy Pauly] We can add a sentence clarifying that just because you asked for it doesn't guarantee you will get it.
  - [Mirja Kühlewind] This seems to require some pre-configuration knowledge about if you need to request an address.
  - [Tommy Pauly] By default, you always request an address. However, if you have a specialized deployment and you know out of band you know your IP (or have some extension in the future) then you know you can omit the ADDRESS_REQUEST.
  - [Mirja Kühlewind] I think this makes your configuration more complex
  - [Tommy Pauly] You can still get an unsolicited ADDRESS_ASSIGN.
  - [David Schinazi] Think of it as DHCP. If you never ask, you'll never get one. If you ask, you may not get the one you want. But if you're using static IPs, you won't ever use DHCP. That's what this is replicating.
  - [Ben Schwartz] Does this add a round-trip before I can send my first packet?
  - [Tommy Pauly] This goes in the same flight as your request. If I'm requesting a specific address, I can optimistically ask for it and send packets optimistically, but the proxy may drop it.
  - [Ben Schwartz] What is the rule for the prefix length?
  - [Tommy Pauly] Capsules have repeated struct with protocol, address, prefix length. In your request you set your desired prefix length. 
  - [Ben Schwartz] I am not sure this is a valid IPv4 or IPv6 address
  - [Tommy Pauly] You're not asserting ownership of the range. It's a request with the address you desire and a prefix length.
  - [Ben Schwartz] Okay, I think this works because there's always 1 IP address in any range that is reserved that is not allocatable.
  - [Jonathan Lennox] I was surprised you can send an unsolicited ADDRESS_ASSIGN. What happens if a client sends that to the proxy?
  - [Tommy Pauly] You ignore it. Or close the stream. We've talked about error handling in general, and some future extension would want more interesting semantics for address assignment. 
  - [Mirja Kühlewind] ADDRESS_REQUEST doesn't guarantee you get the address you ask for. ADDRESS_ASSIGN for whatever you ask for.
  - [Martin Thomson] Will you always receive an ADDRESS_ASSIGN in response to an ADDRESS_REQUEST.
  - [Tommy Pauly] We could always respond. But you need a timeout always.
  - [Alex Chernyakhovsky] I think we need to always respond, even if it's with an error response to each ADDRESS_REQUEST.
  - [Martin Thomson] Particularly for the empty address case, where you don't care, it's useful to answer "am I still waiting?"
  - [Tommy Pauly] If we make ADDRESS_ASSIGN cumulative, then it's unambiguous that we always reply, and they're fully describing. 
  - [Martin Thomson] How do I distinguish between an unsolicited ADDRESS_ASSIGN and the response to my REQUEST?
  - [Tommy Pauly] You don't care, because at worst you will get a duplicate ASSIGN.
  - [Magnus Westerlund] To address something Ben said, I think we need to explicitly assign the address we use for rewriting.
  - [Tommy Pauly] If you know about the address, you can request for it, and you could try sending on it, and there's a chance that the proxy may assign a no-address or different-address in which case you retrasmit. 
  - [Magnus Westerlund] Earlier your response to Ben suggested anything would work. But you need to have high probability of knowing the IP address for it to work.
  - [Tommy Pauly] In site-to-site VPN case where you own the IP, it's self-assigned.
  - [Ben Schwartz] I still am not sure this works. If I am trying to optimistically set my src to all-zeroes ---
  - [Tommy Pauly] That requires an extension. If I've previously opened streams and had IP addresses assigned, I can request another stream from the same address and try sending on it.
  - [Ben Schwartz] But there's no reason to expect consistent IP assignment on different tunnels
  - [Tommy Pauly] But you can request it. It makes sense for most implementations to try to reuse addresses where possible.
  - [Alex Chernyakhovsky] 0-RTT for full VPN is not really a goal. For the more targetted CONNECT-IP tunnels it's likely client already has concurrent connection open and proxy server will reuse the same IP as requested
  - [Ben Schwartz] But then you should say SHOULD
  - [Alex Chernyakhovsky] I believe we already do. Also note that you may lose a packet always, so the ICMP error is not guaranteed.
  - [David Schinazi] With the address assignment being cumulative, you can also remove addresses. 
* [Issue #54](https://github.com/ietf-wg-masque/draft-ietf-masque-connect-ip/issues/54)
  - No comments

# Individual Drafts (30 minutes)
## HTTP Access Service Description Objects (Ben Schwartz)

* [Martin Duke] Proxy discovery was out of scope in this charter. We can recharter, but I am wondering if this is the right venue.
* [Tommy Pauly] Thanks for bringing this up. I feel like I should like this direction, but I am not convinced of this particular shape. It's combining too many things in 1 JSON blob. Too much information that's not specific enough, but also not enough to be fully useful for each use case. I think this makes sense for things that proxy e2e connections, but not OHTTP and DOH keys.
* [Tommy Pauly] Your key rotation scheme for each service is likely distributed and different, and now with this flat file you'd have to worry about all the services. We also won't have open proxies for these, as they also need authentication to be usable.
* [Jonathan Lennox] I am dubious you can combine 4 WGs configuration languages.
* [David Schinazi] I can kind of see how this is useful, but I can't see anyone actually using it.
* [Ben Schwartz] The use case I am targetting is the `.well-known` template for CONNECT-IP. These things break the HTTP model of requests. It also means to find the capabilities of these things you need to send a lot of probes. I can imagine going into system proxy settings and setting up OHAI and DOH. There's a standard and then the phoen knows how to use it.
* [David Schinazi] That is a description of what you can do with this, not what people will do with it.
* [Ben Schwartz] I'd like this for Android VPN settings.
* [David Schinazi] Since this touches on DOH, OHTTP, DNSSEC, I don't think this is necessarily the right place. I think httpbis may be a better fix. Jonathan said dispatch and I agree with that.
* [Mike Bishop] I respectfully disagree that this is not discovery of the proxy -- this is discovery of the proxy's capabilities once you've been configured for a proxy. Who even knows what those things are outside this building? I think this is useful, and the question of crossing groups is solvable with IANA registries and supported services at this hostname. Does it belong here or dispatch? All is reasonable.
* [Alex Chernyakhovsky] Agree with Mike. This is a question on capabilities more than discovery. Also CONNECT-UDP and CONNECT-IP proxies may frequently be hidden and/or require authentication. So this capability may vary based on the user.

## Proxying Listener UDP in HTTP (David Schinazi)
* [Jonathan Lennox] Is this sufficient to run an H3 server through this?
* [David Schinazi] Yes.
* [Tommy Pauly] This looks simple and straightforward. Problem with CONNECT-IP is you don't have a port allocation service. So I think this is cleaner in that regard. It's also the most compelling extension that uses a context id.
* [Ben Schwartz] I think this is not as simple as it looks. This is basically TURN. We should take a good look at TURN. Address restricted NAT.
* [David Schinazi] This doesn't do all of TURN. It's a NAT, where you'er sending things to the MASQUE proxy which is doing the mapping. Just 1 NAT binding.
* [Martin Duke] Server initiated services are out of scope per the charter.
* [David Schinazi] MASQUE client is asking for this.
* [Lucas Pardue] I think this extension is neat and fairly supported on top of what we already have. I think it doesn't fit the charter as written, so that's good for thinking about the charter conversation.
* [Mirja Kühlewind] Nice hack. Why not extend Connect-IP to do port assignment.
* [David Schinazi] Then all of MASQUE is a hack.
* [Magnus Westerlund] I think the biggest question here -- TURN had endpoint filtering for signalling. 

# Recharter discussion (Time remaining, 8m)
* [Eric Kinnear] We've made great progress. MASQUE is not intended to be a long lived working group. Where is the boundary where we've done enough in MASQUE, whereas maintenance can be deferred to the other groups.
* [Eric Kinnear] We need to decide within our charter if we want to name explicit extensions? Do we instead want to offer criteria for extensions? Do we want to be more open ended?
* [Eric Kinnear] Core principle we are going for is including things that we need to make masque work for real in the real world with real people.
* [Eric Kinnear] This has one big asterisk around discovery and proxy discovery, because real people need to do that.
* [David Schinazi] I'd love to see our AD in the queue. My general take is when you start new work it's very important for the charter to be extremely tight. Now that we're done with those, deciding what to work on next depends on who wants to do the work, write documents, implement, use it. I don't think we need the help of the charter to scope us down. If we tighten it down too much it would get in our way. So I would suggest to be open to extensions to existing work as well as discovery. Our gate for if we work on something or not is the adoption call.
* [Tommy Pauly] Opening up for extensions is important. I agree we should not name specific extensions. It may be worth mentioning in the charter suggested areas. UDP listen was clearly missing before. We need to exercise our extension points. Compression, forwarding. ORTT extensibility on requests. Timebox before we re-evaluate so we aren't long-lived. I'm OK with leaving discovery out but can be convinced otherwise.
* [Spencer Dawkins] What did you mean about handing over maintenance?
* [Eric Kinnear] That's where we are looking for input, when do we hand over?
* [Spencer Dawkins] We've discussed location a bunch, and discovery was out of scope. Someone needs to discover something somehow at some point
* [Ben Schwartz] No one knows what discovery means. Configuration is important and should be in scope. Client authorization is important and should be in scope.
* [Magnus Westerlund] I support this. Extensions that we will be writing. Give us some time to think.
* [Martin Duke] I am inclined to re-charter. I don't have strong convictions on the contents of the charter. Extension proposals will be forever. We need an off-ramp.

