# MASQUE at IETF 119

Notes by mt

## QUIC-Aware Proxying

_Tommy Pauly and Eric Rosenberg, 25 minutes_

Eric R. not here, so Tommy is winging it.  YOLO
Tommy gives a recap of the protocol.

Pro Tip: CID stands for connection ID.  RFC 9000 does not define this shorthand, but somehow we all know what it means.

### Design Team

Formed to look at traffic analysis on the forwarded mode.  Then proposed a mechanism for encryption of forwarded traffic.

"scramble" transform uses AES to prevent simple matching.

Lots of caveats.  These are documented.

No padding.  No chaff.

Brian Trammell (from the bottom of a large toilet): do transforms map m to n rather than 1 to 1?

Tommy: Yeah, we only define 1:1 transforms, but that is not a constraint.

Martin Duke: In tunnel mode you can add padding easily.  Here you can't pad.

Tommy: The proxy can't pad in a way that only affects one leg.

Martin: This is a constraint.

Tommy: Sure, but we could define a new transform.

Martin: Can you repeat / chain transforms?

Tommy: (visible confusion) I don't think the semantics make sense...

Ben Schwartz: We model this on Accept, so client advertises many, server picks one.

Martin: Simple transforms can be composed. 

Tommy: You could also define a composition as its own transform? Realistically there are sets of these that are internally composite. you don't need arbitrary composition.

Martin: You can for reframing, adding a length then putting the packets in one larger packet.

David: please do a single transform.

Mirja: +1. Single transform please.

Tommy: Transforms come with a parameter.

CHAIR: Let's run a call on the design team output.

 Do we have implementations??
 
Some hands raised:
Ericsson (transforms @ hackathon)
Google
Apple

### LOOOOOOOOOOOOOOOOOOOOOOOP

Preconditions are a little complex.  Evil clients can arrange to have a proxy forward to itself, which creates an infinite forwarding loop.  With no packet size expansion as a result of encapsulation, this is a wonderful DoS attack on the proxy.

Solution in PR 104 proposes that the proxy choose connection IDs.  That breaks the loop.  Some extra complexity.

Alex: The solution before was that we can't ensure that the client uses the connection ID provided by the proxy.

Tommy: This is the connection ID on which the client receives packets from the proxy.

The loop is downstream packets.

Martin: The client sets all the virtual CIDs in both directions, so it constructs CIDs so that the two proxies point at each other.

Mirja: Are you applying this if you think there is an attack?

Tommy: This would change what you do all the time.  It adds a capsule, but not time. Mappings and forwarding rules only set up after negotiaion anyway (in our impl at least)

Martin: on the vcid side of things... 

tommy: proxy chooses both. before fixing loop, client and proxy each choose what they receive on. change here is that client can ack/veto, 

martin: this constrains the client port allocation strategy space. yes.

david: it's a mess in all cases. in QUIC all endpoints choose what they receive on. with quic-aware, client packets can come out of the proxy's address... whichever way you look at it it's wrong for someone.

Tommy: even without forwarding, the proxy receives packets on a client-chosen cid

david: proxy now gets packets aimed at the target...

tommy: this happens in all quic-aware cases.

david: maybe we should take this offline in a smaller group

tommy: ugliness is payback to the client side. we're taking the proxy's complexity and forcing the client to deal with it too.

mirja: collisions?

tommy: client vetos, next round.

martin duke: clients can't route by cid. 
Is that going to be true of up/down... earlier proxies on their target-facing interfaces, do we not think that they will need routeable(spelling?) connection IDs.

Tommy: Clients can only choose to share ports if they can guarantee non-conflicting CIDs.

Martin D: There is no veto on a CID in CONNECT-UDP.

Tommy: CONNECT-UDP requires a unique client socket for each.

eric: let's try to get interested folks together in the hallway to converge on this.

## ~Proxing Listener UDP~ CONNECT-UDP Bind

(same thing, new name)

_Abhijit Singh, 25 minutes_

Ted: for v6 does it make sense to also permit a prefix? a host might be using a whole prefix. Privacy addressing does change outbound addressing, sometimes often. Not very v6-ish 

David: The goal is to let the client give out an address to others.  You need to have a concrete address to give out.

Tommy: This is not communicating the public addresses, it's the return status from a bind() call.  You might get a different address from the next bind().  Should this be just a proxy status parameter?

Abhijit: That might work.  We had to ask ourselves if we fix the address for the connection/session duration.

Jonathan: Regarding privacy addresses.  Should there be a flag to ask for a completely new address?

David: Why?  It's a pool of addresses that is shared.  You don't need a uniquely new address.

Lucas: What is the form of this header.  Structured fields!  Having v4 and v6 keys.

David (off mic): read the doc

Lucas:  This could be better defined.

Abhijit: Tommy suggested both compression and IP restriction stuff.

Define new capsule types: compression_assign.  Capsules can refer to this.
Setting IP version = 0, you can reach any target, but you have to specify everything.
compression_close terminates a compression context.
Proxy can close contexts too (sounds like a race condition waiting to happen).

Martin: having close act from both endpoints doesn't really work in QUIC. No half-close, there can be disagreement about underlying state. One of these two has to be treated as a request. If I created it, I should be the one to close it. If the proxy can close it while I'm still using it, that's a problem

David: You can treat your state as unidirectional (even it the ID is the same in both directions). Close is sent with cancel, so they'll eventually get there.

Martin: Might get reordered. So both sides have to close.

David: Yep.

What if they don't? We have ways in protocols to clean this up.

Abhijit: COMPRESSION_CLOSE has a response. Context ID is echoed back.

Ben: Hard to manage garbage collection for this.  I don't want to block packets, but I don't want to waste space on this.  Need a clear distinction between opening and closing and blocking certain categories.

...: In terms of state machine, I should be able to kill some contexts and recover state later.  It should not be a case that I can't get back to a previous state.

Tommy: interesting, looks good.  A little cute.  With a header, you establish a context.  0 means no connect-udp.  Why do we need header and capsule?

(echoed in chat: Jonathan Lennox: I don’t love COMPRESSION_CLOSE meaning three different things depending on context. Are capsule numbers scarce?; Benjamin Schwartz: In general capsules' error handling is woefully underspecified and we're just hoping they don't fail)

Abhijit: The value could be the context ID.  That might be removed.  Will consider it.

Tommy: There should be just one way to get a context ID.

Mirja: The close seems more generic.  If it is only to clean up state, but maybe you can just move on and make a new session.

Abhijit: the IP restriction part is beneficial here.

## Proxying Ethernet

_Alejandro Sedeño, 25 minutes_

### MTU and fragmentation

Yaroslav: VXLAN comes to mind.  There, things are dropped if they exceed the MTU.  MSS clamping at endpoints might be needed.

Brian: Encapsulate IP over Ethernet, or is this for non-IP.

Alejandro: might be general, but have ideas about making it better for IP over Ethernet

Alex Chernyakhovsky: Maybe make some recommendations about what you might do.  Like encapsulating full-sized frames in streams.

Ben S: The logically correct thing is that we expose the right MTU off that interface.  The normative thing is that metadata should (mumble).

Alejandro: bridging can be complex, the path MTU can change on the fly, will be a problem

Lars: DO the minimum thing possible, but don't build the perfect thing.  Maybe we could also drop this proposal becauase it is much harder than we anticipated.

Brian:  +1 to Ben and Lars.  I would add that we don't add fragmentation at this layer.  Fragmentation is not a solution, it is a problem factory.

Mirja: We should not describe these.  BY default, drop the packet.  There might be MASQUE layer hacks.

Christian Huitema:  Sending messages that are larger than the MTU is very common in media over QUIC.  There we decided that we would use short streams.  That might be a better deal than trying to use fragmentation.

Zahed: we should not try to solve this with engineering (?)  I don't know if we have enough expertise to judge whether dropping frames would be acceptable.  Please involve others who have already done this sort of work.

Alex: To address Christian.  We used short-lived streams in QBONE.  We can't do that here.  We can't make new streams easily.  That doesn't mean that we can't do capsules instead.  The networks that we interface with have an MTU, so we need to be consistent.  The process of dropping frames might have unknown consequences.  We need more information to answer the question.  Implementing and more input are needed.

### MAC Addresses

Do we do something like IP assignment?

Yaroslav: this seems like implementation detail, unless we're doing filters.

Lars: Propose that we do nothing unless we have strong evidence that something is needed.

David: Happy to have this deferred to an extension, unless there are cases that don't work at all.

### E-VPN

Alejandro: defer this one

Eric (chair): If we have use cases, we can talk about it then, folks need to come forward and describe those first.

### Skipped some isses

### Frame delivery order

PWE3 has config for delivery order, except when doing link aggregation. Maybe we don't care.

### Frame Check Sequence

Tianji: The proposed outcome might cause problems.  Will take this offline.

### PAUSE

PWE3 doesn't pass these, maybe we shouldn't either.

(Off mic) don't send them.

### VLAN stuff

Should we do more?  (no answer)

## In-Band DNS

_David Schinazi, ~10~ 6 minutes_

DoM ? DoC ?

Martin: Not sure that I'm happy with DNS over Yet Another Thing. Why is DoH not applicable here?

David: This is about setting up a tun and exchanging DNS configuration so that the DNS can tunnel as well

Martin: Just give them a DoH endpoint. 

David: The other side of the tun is an OS, which might not DoH.

Lars: Why not just tunnel DHCP, then you get everything?

David: We already decided not to do that. Not wrong, just not in line with our current design.

Tommy: This is fine. Didn't read the draft because you posted it epsilon ago. We have a bunch of associated stuff in IKE, ADD/DNR, if you want to sign up to do all that stuff, cool.

Mark: vpns are just another link type....

David: <fast talking>  RA <fast talking> i'm not old enough for ikev2

Ben: We should weigh that (???) against this (???).  (Something about DHCP RA modes.)