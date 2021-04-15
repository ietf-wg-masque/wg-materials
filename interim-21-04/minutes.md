# IETF MASQUE Working Group Agenda - Virtual Interim - April 2021
Thursday, April 15, 2021 (UTC)
**19:00-21:00 (UTC)**

[See this time in your local timezone](https://www.timeanddate.com/worldclock/fixedtime.html?msg=IETF+MASQUE+Virtual+Interim+-+April+2021&iso=20210415T19&p1=1440&ah=2)

* [Jabber Chat](xmpp:masque@jabber.ietf.org?join)
* [WebEx](https://ietf.webex.com/ietf/j.php?MTID=m0b9ea8ca8afd32ab1f64ab5a648cbdd2) (Meeting Number: 185 878 0126, Meeting Password: EBiHziG5J36)
* [Minutes](https://codimd.ietf.org/notes-ietf-interim-2021-masque-03-masque)

## Housekeeping

* Scribe Selection / [Note Well](https://www.ietf.org/about/note-well.html)
* Agenda Bashing

## Presentations

### HTTP/3 Datagrams, CONNECT-UDP - David Schinazi (60 minutes)
([draft-ietf-masque-h3-datagram](https://datatracker.ietf.org/doc/draft-ietf-masque-h3-datagram/), [draft-ietf-masque-connect-udp](https://datatracker.ietf.org/doc/draft-ietf-masque-connect-udp/)) 

Focusing on H3 Datagram; most CONNECT-UDP issues hinge on decisions to be made there.
Mapping Datagram Identifier to particular requests
Need to support:
- False start
- Intermediaries
- Extensibility (without extending intermediaries)
- Multiple formats for DATAGRAM frames
- Negotiating formats mid-connection

Mirja:  Why do we need to negotiate mid-connection?  You know capabilities up-front, you can select formats on requests.
David:  That's not how our deployment works, since a new request could get sent to a different backend.
Tommy:  Intermediaries make this hard, too.  One request end-to-end is easier.

Ben:  "Negotiating" might be the wrong word.  Available formats are negotiated up-front, but one of them can be invoked mid-stream.
David:Yes, instantiate or invoke, not negotiate.

Let's do the architecture, not wire details yet.
Ben's proposal: two varints, not one.  First is stream ID (hop-by-hop), second is flow ID within the stream (end-to-end).

Jana:  Is the flow ID a request ID?
David: One request has potentially many flows inside it. They might be allocated to extensions, or allocation might be negotiated.
Jana:  More like a context ID, then.
Matt:  Yes, let's not overload flow further. (via chat)

Tommy: +1 to design, this is where I ended up as well.  Better for intermediaries.

Mirja: +1 to design; please don't call it flow ID.  Maybe just "context"?  Extensions could assign semantics further.
David: Identifier points at something; that something could have more information.

Kazuho:+1 to design; also simpler.

Luke:  +1 to design, but maybe we could negotiate away the wasted byte when there's only one context.

Alex:  +1 to design; "flow" is bad, but "context" is even worse.  But it is an ID; extensions shouldn't try to allocate bits.

Martin Duke: Is the flow/context unique? Can different streams use the same one?
David: Stream-local; different streams could allocate differently or the same.
Martin:ECN needs to be per-hop, if I'm following properly. How would I use an end-to-end field for that?
David: Multiple ways to solve this, but depends who's terminating the connection.

Jana:  "Two-layer" design makes sense, but not sure that's a good name.  Stream
       IDs for routing, context IDs for semantics.  They're orthogonal, not layered.

Are flow/context IDs namespaces joint in both directions (bidirectional), or
separately controlled (unidirectional)?

Lucas: Bidirectional might break down in cases like compression.
David: Trouble with unidirectional is that you can't reject registrations. Some extensions want to do that.
Lucas: Can be achieved without being bidirectional; just need a mechanism for rejection after the fact.

EKR:   Bidirectional is simpler, matches implied semantics that client decides and proxy obeys.
David: Allocate some to each party, so initiator decides.
EKR:   Then they're effectively the same; bidi has disjoint namespaces with the initiator's bit.
EKR:   Nervous about compression state in this. Memory can blow up when we tie state to integers.

Ben:   Same DoS vector problems; that's orthogonal. +1 to EKR, this is essentially a compression context to remove information from payload.
David: Some cases have additional information.
Ben:   Could be modeled that way, though; extra state could always have been included in payload. Representing as compression is valuable.
       Don't think we need rejection - agree at the beginning what you support, and peer can use anything you support.
David: DoS vector in that. Really don't want to need flow control to mitigate that.
EKR:   Don't I need ack from the other side before I can use one of these?
David: False start -- assume it works, and recover if it's rejected.

Tommy: Bidi/uni is separate from accept/reject of flows; bikeshed, but prefer
       bidi. Disagree with Ben that everything is compression and rejection is
       unneeded. HTTP has rejection; so does QUIC. We need it here.

Alan:  What is the lifetime of a stream/flow ID compared to the QUIC stream it's tied to?
David: Need to decide, but I think only while you can send on the corresponding QUIC stream.
Alan:  Race between DATAGRAM and FIN becomes an issue then.
David: File an issue; need to discuss.

Kazuho:In QUIC, we discussed bidi/uni and eventually chose "both". Learn from history.

Negotiating flow IDs.  Currently uses header in request, but can't be used mid-stream.
Consider a design that adds a registration message mid-stream?

Alex:  Support message; need mid-stream support.

EKR:   Same issue with timing as previously.
David: Yes, still need to answer that and rejection.

Kazuho:Two-layer design means intermediaries don't need to understand the flows, just forward them.
David: Intermediaries just copy bytes here.

Alan:  Can endpoints pre-agree on flow IDs if the endpoints are just forwarding?
David: Can't do on-the-fly compression that way.
Alan:  There are other use-cases, though. Timestamp is easy to pre-allocate.
David: Adding static to dynamic is easy; adding dynamic to static is hard. Also need to consider false-start.
Alan:  You wind up with two layers.

Mike:  Priority decided both header and frame.  H3 frames are hop-by-hop; have to mandate forwarding.

Is flow ID optional? Possible way to designate something as the *only* flow.

Ben:   This should be implicit; you know whether you need many flows or only one on a given stream.
       But if the DATAGRAM arrives before the request, you can't parse it.
David: That's always true, regardless of what we decide here.

Jana:  Need some kind of registration.

EKR:   You don't understand False Start. Figure out what we mean by that. One possibility is that
       they depend on a request succeeding, and are irrelevant if it doesn't.  Another is that they
       might be retransmitted if they're lost/fail.  That will affect desired behavior at the other
       side.
David: Those reduce to the same problem. Attempt to send; if it fails, decide what to do next.
       Optimistic sending, recover if dropped.
EKR:   In one case, it can be handled at the MASQUE layer; in the other, it has to come from a different layer.

Lucas: Easy to do a one-shot CONNECT-UDP, send Initial, and move on.  If it gets dropped, that's fine.
David: Yes, still have to handle packet loss.

Alex:  If you can't afford to have it lost, don't send it in a DATAGRAM. If you use a DATAGRAM, you need
       a mechanism to recover.
       
Didn't really discuss optionality.

Registration -- reserved flow ID or separate frame?

Ben:   Bikeshed; move on.  I don't like either.

Control messages -- is REGISTER the only possible control message, or have extensibility?
Probably don't need it, but extensibility end-to-end is interesting.

Do we need a RELIABLE_DATAGRAM frame?

Jana:  Figure out desired properties of control messages first, then map to QUIC primitives. "Reliable datagram" is a double negative.
David: Can't just be DATA because it's not part of the request/response.

Mike:  This concept is basically just an envelope for an H3 frame to make it end-to-end rather than hop-by-hop.
       If we need that concept, don't scope it to DATAGRAM only.

### QUIC-Aware Proxying Using CONNECT-UDP  - Tommy Pauly (15 minutes)
([draft-pauly-masque-quic-proxy](https://datatracker.ietf.org/doc/draft-pauly-masque-quic-proxy/))

Looking at ways to modify CONNECT-UDP when you know the tunnel is for QUIC.  Improves perf in various ways.
Key idea is that proxy is aware of CIDs, so it can share ports across many connections.
Especially applicable when you have multiple proxy hops. Some tracking issues if every proxy forwards.
NIC offload enables near-line-rate performance.

EKR:   Surprised by perf difference. You can do line-rate TLS; tunnel-mode can
       probably be made equally fast.
Tommy: Even if an implementation can be made equally fast, it still has advantages with the server.
EKR:   Multi-hop usually has its own threat-model which this wouldn't meet. Need to have exactly why
       you want these properties. Null encryption is generally a bad idea, even if you have other layers.

### IP Proxying Requirements - David Schinazi (15 minutes)
([draft-ietf-masque-ip-proxy-reqs](https://datatracker.ietf.org/doc/draft-ietf-masque-ip-proxy-reqs/))

Any objections to recent issue resolutions?

Mirja: Couldn't exchanging routes be an extension?
David: Can't operate with no routes, can you?
Mirja: If the proxy is rewriting IP addresses, yes.
Alex:  Other use cases require it.
Mirja: Still should be an extension.  Discuss offline.

Address validation
Mirja: Unclear whether we need protocol support for this. Why rule it out?
David: Scoping document requires excluding things.
EKR:   Two modes: Either the server is rewriting IP addresses, or the server is allocating IPs and I use them.
       Are we trying to say only one of these is supported?
David: Also discuss offline. Mirja to add more text on the issue.
Tommy: Clarify that the base document shouldn't do this, but don't foreclose extensions.

Proxying IP packets or payloads?  If you build a solution for packets, an
extension could compress away the headers. If you build a solution for
payloads, hard to add header control via an extension.

Moving to next discussion....

### Requirements Document Remaining Items - Chairs (10 minutes)

Clear support at IETF 110 for proxying packets; less clear on payloads.
Ongoing consensus call on list. Please respond on the list with usecases
and opinions.

Once this issue is resolved, will declare requirements document complete.
No plan to publish document; purely scoping exercise for a future document
we will adopt.

(No objects to this plan raised.)

## Time Did Not Permit
* The CONNECT-IP method for proxying IP traffic ([draft-kuehlewind-masque-connect-ip-00](https://datatracker.ietf.org/doc/draft-kuehlewind-masque-connect-ip/)) - Mirja Kuehlewind (10 minutes)
* The CONNECT-IP HTTP Method ([draft-cms-masque-connect-ip-00](https://datatracker.ietf.org/doc/draft-cms-masque-connect-ip/)) - Alex Chernyakhovsky (10 minutes)



# Blue Sheets

Ben Schwartz, Google

Spencer Dawkins, Tencent America

Eric Kinnear, Apple

Chris Wood, Cloudflare

David Schinazi, Google

Eric Rescorla, Mozilla

Martin Duke, F5

Alex Chernyakhovsky, Google

Tommy Pauly, Apple

Mike Bishop, Akamai

Alan Frindell, Facebook

Matt Joras, Facebook

David Oliver, Guardian Project

Nick Banks, Microsoft

Marcus Ihlar, Ericsson

Mirja Kühlewind, Ericsson

Magnus Westerlund, Ericsson

chi-jiun su, hughes network systems

John Border, Hughes

Lucas Pardue, Cloudflare

Sean Turner, sn3rd

Jana Iyengar, Fastly

Jorge Lopez-Silva, Facebook

Erik Kline, Google

Nate Sales, Unaffiliated

Dragana Damjanovic, Mozilla
