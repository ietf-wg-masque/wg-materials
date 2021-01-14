# [Meeting minutes](https://codimd.ietf.org/notes-ietf-masque-interim-21-01)

# IETF MASQUE Working Group Minutes - Virtual Interim - January 2021

* [Jabber Chat](xmpp:masque@jabber.ietf.org?join)
* [WebEx](https://ietf.webex.com/ietf/j.php?MTID=m60be159911993f0a28d72228e878299a) (Meeting Number: 178 573 8261, Meeting Password: JXuMYDG7h23)
* [Minutes](https://codimd.ietf.org/notes-ietf-masque-interim-21-01)

## Tuesday, January 12, 2020 (UTC)
**19:00-21:00 (UTC)**

[See this time in your local timezone](https://www.timeanddate.com/worldclock/fixedtime.html?msg=IETF+MASQUE+Virtual+Interim+-+January+2020&iso=20210112T19&p1=1440&ah=2)

### Housekeeping

* Scribe Selection / [Note Well](https://www.ietf.org/about/note-well.html)
* Agenda Bashing
* Github usage and draft status
  - Lots of closed issues, but no PRs.  Please use PRs to track changes in response to issues.
  
  David:  This is me, sorry.  I'll be better.
  Mirja:  Please use PRs so we can see what the change was.  Don't close issues until we have agreement.
          Also, please raise design issues to the mailing list as well.
  Mike:   Disagree about keeping issues open after PRs merged, but maybe have a changelog so there's visibility?
  Chris:  Up to the editors, but close issues when the editors think they're addressed.
  Mark:   Don't spend too much time on process; chairs should decide and make sure to document so we're all on the same page.
  Alex:   Tried to include reference to the issue in the commit that referenced it.  Do we need people besides the editors approving before they merge?
  Mirja:  Discussion on the issue, ideally including the issue opener.
  Eric:   Let's move on.  We'll add guidelines to the repo.
  Mirja:  Please add additional editors in this case.

### Presentations

#### Using QUIC Datagrams with HTTP/3 ([draft-schinazi-masque-h3-datagram](https://tools.ietf.org/html/draft-schinazi-masque-h3-datagram)) - Lucas Pardue

Lucas added as co-author
Datagram-Flow-Id header moved into (renamed) draft-schinazi-masque-h3-datagram draft; adopt it?
  Martin Duke: Is datagram an HTTP/3 frame or a QUIC frame?
      QUIC frame, but draft defines interpretation within HTTP/3.

DFI header is now a list, not a single value; optional names for each ID
  Martin Thomson:  How does the name not conflict with other extensibility?
  David Schinazi:  Names allow assigning flow IDs to different extensions; registry in doc.
  Martin:  That's even worse.  Will open an issue.
  David:   Able to interpret this even if you don't know the extension using it.
  Martin:  What about a parameter called "name"?
  Mark:    +1, this is pretty byzantine
  David, Lucas: Sure.
  
  Ben:     Uniqueness doesn't seem required either.
  David:   Current extensions don't need more than one ID, but we could remove the requirement.
  
  Ekr:     Some repeatable and some not is hard to validate.  Pick one.
  Tommy Pauly:  Repeatability issue goes away if we have parameters instead.
  Lucas:   Want to be able to have parameters for extensions, but there are lots of possibilities.
  Mirja:   Proposal assumes an extensibility design; let's figure out extensibility first.
  
  Ben:     Type-safe-looking parameters is potentially a layering issue.
  David:   Registry seems to address this today.
  Ben:     That puts totally different things having to register together.
  David:   That's why we have registries, right?

Flow ID retirement, reuse
  Ekr:   We have 2^62 flow IDs; why do we need reuse?
  David: File an issue if there's not already one.

Adopt?
  MT:    CONNECT-UDP currently requires declaring flow IDs up-front.  Can we use frames for that instead?
  Lucas: Similar concept to Tommy's QUIC-Aware Proxying, fixing up the headers subsequently
  MT:    Many ways to spell this.  Doesn't block adoption.

  Victor:Concerned about the change of venue, and the fact this document keeps growing new features.
  Chris: HTTP chairs referred it here.
  David: It's an individual draft, I've been changing it as I have ideas.  If it's adopted, that changes.
  
  Mirja: Would like to see naming changes incorporated before adoption happens.
  
  Poll for ability to use poll:
      Yes 21/34
      No 1/34
      Just here for slides 10/34
  Poll for adoption:
      Yes 21/34
      No 1/34
      No Answer 12/34

  Lucas:  Assuming adoption happens, migrate the document first and then open issues.
  David:  Put this in its own repo, so we can migrate the repo to the WG and bring issues along.

#### CONNECT-UDP ([draft-ietf-masque-connect-udp](https://datatracker.ietf.org/doc/draft-ietf-masque-connect-udp/)) and GitHub Issue Discussion - David Schinazi

#16- Moved to H3-DGram draft; allows 1:1, 1:many, and many:1 relationships

  MT:    Intermediary might route flow IDs easily, but many:1 could get messy
  David: Don't over-index on intermediaries; they'll probably be uncommon.
  MT:    It's an HTTP method; we need to work within the HTTP architecture. Why do we need this?
  David: Let's wait for Tommy's presentation for justification.

  Alex:  Also concerned about many:1 based on implementation experience.  Breaks horribly in unexpected ways.

#8 and #23- Use "https" as our scheme?  Scheme doesn't actually carry information here.

  Mark:  Why is defining a new URI scheme hard?
         "https" could work, there's precedent, but a separate URI scheme is clearer
  David: A separate URI scheme is interesting for configuring proxies, but this is the
         reference to the endpoint.
  Mark:  HTTPS could actually work.
  
  EKR:   The semantics here aren't wanting to do HTTPS, they're sending UDP packets.
         "https" doesn't make sense.
         Was it a mistake to create a new method if the exception can't be extended?
         Just do CONNECT and define a header that changes semantics.  You'll learn in
         the response if the proxy doesn't support UDP.
         
  Magnus:Whatever we do here, know how we'll handle IP, other protocols, etc. that we might
         want to proxy in the future.
  
  MT:    New URI scheme is a little hard, because we have to understand what it's conveying.
         This is just a means to an end; getting through HTTPS. It's ugly, but works.
  
  Tommy: "https" is more right than defining a new URI scheme. But why not use the scheme
         of whatever the client is trying to reach?
 
  Ben:   Using "https" requires updating the definition of that scheme.  "udp" is better,
         or maybe "null".

#15/#24/#28- Stream Format
Need to delimit datagrams on-stream when QUIC DATAGRAM not supported
Now uses sequence of TLVs, possible extensibility point

  EKR:   Why do we need this alongside the multiple Flow ID stuff?
  David: Have to support fallback to stream when DATAGRAM isn't supported. Would like
         mechanisms to align, but haven't come up with better option.
  
  Magnus:<...>
  
  Tommy: Can we just Use flow IDs as the identifiers of the chunks?

#1/#3- Intermediaries

Flow IDs are negotiated per-hop and requires negotiation on each hop.

  MT:  Is the intermediary required to do anything if it appears on another method?
  David: Remove it if the application of Datagram-Flow-ID to the method is not understood by the intermediary.

Please review Performance Considerations and file issues if they're not sufficient.


#### IP Proxying Requirements ([draft-ietf-masque-ip-proxy-reqs](https://datatracker.ietf.org/doc/draft-ietf-masque-ip-proxy-reqs/)) - Alex Chernyakhovsky

Not reviewing for time's sake.

#3- Why would the server request an IP?

  Mirja: Why does this need to be part of the core document and not an extension?
  Alex:  Doesn't make sense -- it would gut this document to remove it.

#5- OAuth is an example, not normative
#9/#11- Encourage the core MASQUE protocol to be as stateless as possible
#13/#10- Loosened text to enable extensions that modify packet format; MUST be capable of sending unmodified

  Mirja: Not sure we have to require this; there are use-cases for not sending
  Alex:  Sure, that's something else you can add as an extension; this doesn't preclude other modes as extensions.
  Mirja: The requirement is still too strong; everything you want can be an extension.
  Eric:  Move this to the issue.
  
  Magnus:Is this byte-for-byte or simply conveying information?
  Alex:  Byte-for-byte should be possible, but other modes are possible.
  
  EKR:   Since we're encrypting, there will always be transformation.  We should be talking about
         transferring all the information.
  Alex:  Yes; if the document doesn't say that, submit a PR.

#16- Term "IP proxying session" is gone

Open:
#12- NAT?
#2- More cross-references between use cases and which requirements result from various use cases


### As Time Permits

#### QUIC-Aware Proxying Using CONNECT-UDP ([draft-pauly-masque-quic-proxy](https://datatracker.ietf.org/doc/draft-pauly-masque-quic-proxy/)) - Tommy Pauly (5 minutes)

Want to improve proxy reuse of 4-tuples, avoid double-encapsulation overhead
Inform proxy of client/server CIDs; if proxy approves, directly forward packets with that CID instead of unwrapping

Proxy has to map CIDs to UDP sockets; can reject in case of conflict/overload

Questions to the list.

#### Additional IP Proxying Requirement Considerations - Mirja Kuehlewind (5 minutes)

Two possible design approaches for IP proxying; requirements doc MUST NOT assume either solution.
