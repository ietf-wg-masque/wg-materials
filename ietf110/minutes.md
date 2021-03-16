# MASQUE Working Group Agenda - IETF 110 Online

* [Jabber Chat](xmpp:masque@jabber.ietf.org?join)
* [Meetecho](https://meetings.conf.meetecho.com/ietf110/?group=masque&short=&item=1)
* [Minutes](https://codimd.ietf.org/notes-ietf-110-masque)

## Tuesday, March 9, 2021 (UTC)
**17:00-19:00 (UTC), Tuesday Session III**

[See this time in your local
timezone](https://www.timeanddate.com/worldclock/fixedtime.html?msg=MASQUE+-+IETF+110&iso=20210309T17&p1=1440&ah=2)

### Housekeeping

* Scribe Selection / [Note Well](https://www.ietf.org/about/note-well.html)
* Agenda Bashing

(no bashing)

### Presentations

#### Working Group documents (as much time as needed)

* HTTP/3 Datagrams
([draft-ietf-masque-h3-datagram](https://datatracker.ietf.org/doc/draft-ietf-masque-h3-datagram/))-
Lucas Pardue 

What are Flows? Use case is demultiplexing.

[Description of Flow ID, see slides]

David Schinazi: Anyone have issues with this design approach.

[Nobody did at this point]

Benjamin Schwartz: If nobody has a use case at this point, can we leave this for later

David Schinazi: If we want to define this as a datagram we need to state this now so someone cant define a datagram as something starting with an emoji or the like.

Benjamin Schwartz: What happens if someone starts sending datagrams today?

David Schinazi: Receiver can do what it likes as it is undefined, hence the need to define it.

Alex Chernyakhovsky: Use case of applications may not need multiplexing seems like an antifeature, overhead of a few bytes for multiplexing is not much. 

Lucas Pardue: We might collide with other possible extensions.

Alex Chernyakhovsky: Understand the point but disagree.

Mike Bishop: H3 does not prohibit datagram, H3 does have a difficulty with negotiating capabilities. In particular when capability sets overlap. Sympathetic to do you have flow ID or not and if you have datagram there is always a flow ID.

David Schinazi: Al the flag says is an entity supports the spec.

Tommy Pauly: Flag may be useful for per hop signalling, lower level may support datagrams but the H3 layer might not..

Jana Iyeng: Lets not overengineer this, build for our current use cases. This clearly doesn't block new use cases in the future.

Martin Duke: Is this just a constraint from the current status.

David Schinazi: This is just saying that there is a capability, not that the capability will be authorized for use.

Martin Duke: Does this lead to security considerations?

Eric Kinnear: Yes, all the setting says is the server can do datagrams not that it will.

Christian Huitema: Concern about privacy issues. A big sign saying that there is a proxy would be bad. Would prefer to renegotiate setting after a user is connected.

David Schinazi: Will review in the security considerations.

Lucas Pardue: [Slide 5]

Tommy Pauly [Slide 6] Multiplicity

Design decision, reuse the HTTP request stream ID or add one in a distinct namespace.

Ian Swett: Reusing flow ids may be an actively harmful. Not yet seeing a justification for the extra complexity of multiple flow ids.Extra level of indirection needs justification.

David Schinazi: The implementation complexity is not large, about 10 lines of code.

Tommy Pauly: Would prefer to not need the first item in a datagram be a repeat of data from the enclosing context. The flow ID semantics are coming from the lower layer, not a quic stream identifier. The datagram ID is not really same thing as a flow

Luke Curley: Useful to look at analogies, RTP and RTCP have identifiers, would be useful to have more examples of utility of multiple flow IDs.

David Schinazi: The reason it is useful is that we often have characteristics bound to the lifetime of the stream. For example VPN tunnel with compression, want to tear down the tunnel at same time as the flow.

Alex Chernyakhovsky: We need this concept should have put it in QUIC, flowID may not be the right term. We do need the extension point.Would like to have Connect IP and WebTRC over one H3 connection.

David Schinazi: Are you arguing for one solution over another?

Alex Chernyakhovsky: We absolutely need this, distinct namespace is simpler.

Tommy Pauly: Clarifying QUIC argument, In quic a stream is defined by something that has transport layer properties. These bytes mean something different. Dont need to commit to what they mean.

[Show of hands: Do you support Datagram Flow ID namespace] 20/1/97

[Do you support flow ID = stream ID (option 1)?] 1/21/97

Eric Kinnear (chair): Seems like Datagram Flow ID wins, will confirm on the list.

Lucas Pardue: [Slide 7] Supporting multiplicity with SF-list


Martin Thomson: In the first one you have to understand the semantics of the name parameter to understand the flow. 

David Schinazi: Idea is that you can have a list of multiple flow ids in same stream. Proxy may not understand the extension.

Martin Thomson: Is this the client saying use sss markings

David Schinazi: Its a handshake... 

Martin Thomson: Default doesn't make sense then because if you don't know the semantics you don't know what you signed on for.

Lucas Pardue: If we go this route, need to specify what happens if you don't understand default

David Schinazi: Need graceful fallback.

Martin Thomson: But you accomplish the same in flow 42 without saying anything. Timestamp option shows issue, if you don't understand timestamp and that is the default, then default breaks you.

David Schinazi: You need to choose a default that is going to work. For ECN could state that if you don't understand ECN then its ECT.

Victor Vasiliev: Observing that the annotations aren't addressing the use case of mapping the http semantics, shoulf these be put into a header instead.

David Schinazi: This is needed for intermediaries. Intermediary needs to be able to parse the datagram id numbers as it might rewrite them.

Mirja Kühlewind: The flow id is being overloaded. All we need is the endpoints understand same semantics. 

David Schinazi: Would be interested in alternative design.

Mirja Kühlewind: If you need ECN you need four code points

David Schinazi: Need to know which is which. And HTTP will reorder your headers. (Note from Julian on Chat: not true for same-named fields!)

Lucas Pardue: Issue #24 Pick an option.

Benjamin Schwartz: based on jabber discusion, we are not close to consensus

[Chair] take to the list then.

Lucas Pardue: Issue #22 Reusing flow IDs is racy. 

David Schinazi: Feature is complex, and dangerous, if people are in agreement, just get rid of it?

David Schinazi: Person who asked for it, asked for its removal... so should we get rid of it and move on?



* CONNECT-UDP
([draft-ietf-masque-connect-udp](https://datatracker.ietf.org/doc/draft-ietf-masque-connect-udp/))-
David Schinazi 

[Slide 2,3,4]

Kazuho Oku: In HTTP a server is allowed to wait for the entire request until they start to see a response, this can result in a race condition. 

David Schinazi: This is an issue, What do folk think

Benjamin Schwartz: This is not an issue as you only go for Connect UDP servers, you know it will support it.

Martin Thomson: Its not a problem, this is just another method.

David Schinazi: Which is the right thing?

Martin Thomson: Intermediary may just end up sitting there hanging.

Jana Iyengar: Could you elaborate on sticking out

David Schinazi: Masque is doing a lot of cool features that are not like a regular HTTP server but look like regular HTTP. We want to be able to prevent a censor being able to discover the magic functionality by probing.

Jonathan Lennox: This is not a huge win, you only find out you are talking to the wrong server a few seconds earlier.

David Schinazi: Lets take this to the list.

David Schinazi: Issue 23 Slide 5

Ted Hardie: This is a bikeshed. Since we don't use the URI scheme, can use anything.

David Schinazi: I don't care as long as I don't have to register.

Kazuho Oku: We don't need a scheme name.

David Schinazi: Thats a different bike shed.

Kazuho Oku: That is dependent on settings

David Schinazi: So this is dependent on the last one which we are taking to the list

Tommy Pauly: Do we have to say what the scheme it is you have to use? We can just say you must use one.

David Schinazi: Maybe take this to the HTTP WG?

[chair] Lets break into its own issue so can be done separate like.

David Schinazi: (remaining issues/slides)

* IP Proxying Requirements
([draft-ietf-masque-ip-proxy-reqs](https://datatracker.ietf.org/doc/draft-ietf-masque-ip-proxy-reqs/))
- Dallas McCall

Mirja Kühlewind: Point about option 2 is we are almost there. We may need both approaches.

Alex Chernyakhovskyovsky: Requirements document should not be path dependent on the drafts.

Mirja Kühlewind: For requirements document should keep it open and be neutral

Dallas McCall: Question is what do we want to define requirements for.

Benjamin Schwartz: One requirement is transport should be lossless for any packet present or future. Any attempt to send the metadata on separate channel is likely to end up being lossy.

[Lossy as in terms of the data being changed, not packets lost]

David Schinazi: Losslelss in terms of fidelity loss, not dropped packets.

Benjamin Schwartz: This is why we ended up going the Base64 encoding route in DOH... Another issue is MTU consistency...

David Schinazi: Compression can bite you. We discovered this doing Apple Watchy stuff. Can talk about compression later. Core functionality is we do full packets. We can't be neutral on this issue, we have to decide what the proxy means.

Eric Rescorla: Agree with David. IP packets come in and IP packets go out. Can malke nice words about what happens in the middle.

Mike Bishop: What we are trying to build is a black box. Requirements doc is trying to describe the inputs and outputs. Cannot be agnostic about those decisions. Is the black box supporting arbitrary IP extensions... 

Eric Kinnear: Focus on the use cases that cannot be implemented with either.

Mirja Kühlewind: There are cases where you need the verbatim but other times you don't care about the IP header.

David Schinazi: Option 1 does not commit us to one or the other. You can build the IP header and just not send it, thats an implementation decision.

[crosstalk]

David Schinazi: We seem to be in agreement, 

Mirja Kühlewind: We are arguing about what a requirements document should be.

Magnus Westerlund: Issues on router function

David Schinazi: We are comming to that.

Show of hands

DO YOU SUPPORT DATAGRAM FLOW ID NAMESPACE (OPTION 2)? 26/2/83

Do you support option 2 (proxy payloads)? 11/17/81

[chair] Take it to the list.

#### Individual documents (time remaining)

* QUIC-Aware Proxying Using CONNECT-UDP
([draft-pauly-masque-quic-proxy](https://datatracker.ietf.org/doc/draft-pauly-masque-quic-proxy/))
- Tommy Pauly

(Skipped -- no time remaining)