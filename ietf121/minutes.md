# MASQUE Working Group Agenda - IETF 121

## Time and Date

* November 6, 2024, 15:00 - 16:30, Wednesday Session III ([See this time in your local timezone](https://www.timeanddate.com/worldclock/fixedtime.html?msg=MASQUE+at+IETF+121&iso=20241106T15&p1=78&ah=1&am=30))
* Location: [Wicklow Hall 2B](https://datatracker.ietf.org/meeting/121/floor-plan?room=wicklow-hall-2b)
* [IETF Agenda Link](https://datatracker.ietf.org/meeting/121/agenda/?show=masque)

## Links

* [Onsite Tool](https://meetings.conf.meetecho.com/onsite121/?group=masque&short=masque&item=1)
* [Meetecho Room](https://meetings.conf.meetecho.com/ietf121/?group=masque&short=masque&item=1)
* [Meeting chat](https://zulip.ietf.org/#narrow/stream/masque)
* [Notes](https://notes.ietf.org/notes-ietf-121-masque)
* [Minutes](https://datatracker.ietf.org/doc/minutes-121-masque/)

## Administrivia (5 minutes)

* Blue sheets / scribe selection / [NOTE WELL](https://www.ietf.org/about/note-well.html)
* Agenda revision


# Working Group Documents

## [QUIC-Aware Proxying Using HTTP](https://datatracker.ietf.org/doc/draft-ietf-masque-quic-proxy/) - 
_Tommy Pauly and Eric Rosenberg_

Thanks Eric Rosenberg for doing lots of the recent updates. 

Presentation of updates from version 3 to 4. 

Marten S: Asks if there's support for a BLOCKED capsule. 

Some comments on that it's not that useful since clients have options. 

'Blocked signals' can be useful for detecting issues, often the case in QUIC. 

The signal doesn't need to be "blocked", but rather a preemptive request for a larger window.

**General feedback that some signal is useful. New issue to be filed.**

Issue 113: need to document behavior at preferred addressm migration. 

**No additional discussion.**

Issue 115: Active attack on scramble. 

This is likely only a variation on a theme, and does not need changes in the protocol. No one concerns with that categorisation. **Some documentation describing the issue to be added.**

**Show of physical hands: how many have implemented some version of this?**
A number of hands shown, and one aspirational implementatation :).

**Show of hands tool: How many have read version 04 of the draft?**
23 Yes
14 No


## [Proxying Listener UDP in HTTP](https://datatracker.ietf.org/doc/draft-ietf-masque-connect-udp-listen/) - 
_Abhijit Singh_

Changes since last time are mostly editorial.
One implementation at the Hackathon (only self test so far).

Path forward to WGLC.
Chairs: We need to see interoperability between implementations.

Multiple hands in the room indicate aspirational implementations.

Close issues and get interop tested prior to WGLC. 

Show of hands:
I have read draft-ietf-masque-connect-udp-listen-04 (or a recent version)
Yes: 13
No: 18



## [Proxying Ethernet in HTTP](https://datatracker.ietf.org/doc/draft-ietf-masque-connect-ethernet/) - 
_Alejandro Sedeño_

Some interop at hackathon.

One open issue:
Layer separation when determining what's congestion controlled. 

David suggests that this should be closed as it's use case specific and should not be discussed in this document.

Ben states that this is a common problem for all versions of Masque. A separate, more general document is likely preferred.

Zahed as an individual points out that connect-ethernet is a wire and that congestion control discussions don't belong there.

Magnus relates this back to old pseudo wire disucssions. 

Christian states that there will be cases where more packets go in that what can be relayed to the destination. Some AQM or ECN marks or something like that might be needed, saying nothnig means overflow. 

Congestion control is two parts.. internal plumbing like Christian mentioned. But you could do some circuit breaking thing in order to protect the rest of the Internet. 

Ted reminds us that most wires do not run through proxies with fate sharing. So some guidance is useful here. 

Connect ethernet runs over QUIC and it's congestion controlled. This is the same for connect-ip. Some simple guidance in that doc, something similar could be done here, even simpler.. AQM and similar is not typically handled in the MASQUE implementation, there's a TAP interface and it's handled in the kernel. 

Lars points out that there are potential non-ip applications that do fixed rate transfers etc. IP tunnels might not need congestion control, but it's harder to state about L2. 

Discussion continues.. 

Since we proxy ethernet we shouldn't care about layers on top says Zahed as an individual.

Discussion continues.. 

Chairs point out a number of choices to take here. 

**Option 3 on the list: When you see congestion/blocked writes, drop packets and document the behaviour. Remove permission to disable CC. Received several thumbs up.**

Gorry wonders if INTAREA people have looked at how you handle vLANS. That has not been done yet. 

**Chairs will request early review from INTAREA.**

Tianji discusses issues with compression. David acks that this an issue for many of the connect protocols. Should be handled in separate docs.

Show of hands:
I have read draft-ietf-masque-connect-ethernet-05 (or a recent version)
Yes: 9
No: 15

## [DNS Configuration for Proxying IP in HTTP](https://datatracker.ietf.org/doc/draft-ietf-masque-connect-ip-dns/) - 
_David Schinazi_

Updated the adopted version of the document to be aligned with svcb documents. Asks for feedback on whether it looks right and feedback on additional features.. 

Ben Schwartz: points out that internal and search domains have been very difficult to explain in other contexts (like PVD). Suggestion to cut those fields. 

David: Internal domains is a very common use case for VPN. **Suggests more discussion.**

Ben points out that there are several follow up features like DHCP n-dot?

Yaroslav agrees that there are lots of painful inconsistensies in client behaviors relating to Internal and Search domains.. 
Align this with PVDs and proxies document in INTAREA. 

Tommy is also worried about search domains and internal domains. Especially search domains. Split DNS for IKE, there is no search domain property.. Search domains are complicated and feel more legacy. 
This is specific to using connect-ip as a VPN replacement. Proxy PvDs is different, more generic mechanism for proxy selection.. 

**Suggestion: follow IKEv2 split DNS and go no furhter.**

Ben S: No existing specification is sufficient in specifying the semantics of an internal domain. There are a number of nuances on whether the use of the internal domains internally only are hard or soft requirements etc. 

David suggests that this can be strongly defined here.

Next steps: More reading, talk to DNS experts early review from DNS or talk on dnsops mailing list.

**Chairs suggest to start discussion on dnsops list before triggering early review.**

Wave in the room from who has or will implement. A few hands. 

Show of hands tool:
I have read draft-ietf-masque-connect-ip-dns-01 (or a recent version)
Yes: 7
No: 14
