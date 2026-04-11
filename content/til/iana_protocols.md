+++
title = "IANA Protocol Registry"
date = 2024-09-16
description = ""

[extra]
toc = true

[taxonomies]
tags = ["networking", "protocols", "RFC", "DMARC", "SMTP"]
+++

If you've ever found yourself browsing through RFCs published by the
[IETF](https://www.ietf.org), you know it can be a bit of a chore if you're
just researching some tag name or error code. You shouldn't need to read
through the entire spec for a protocol just to do some troubleshooting. Right?
Well, here comes [IANA](https://www.iana.org) to the rescue. IANA has
conveniently compiled a central repository for the many codes and numbers
contained in Internet protocols. You can find it at their [protocol
registry](https://www.iana.org/protocols).

Personally I was just looking for a [simple
breakdown](https://www.iana.org/assignments/dmarc-parameters/dmarc-parameters.xhtml#tag)
of all the tags available for DMARC. Of course, there's also a [DMARC tag
registry table](https://www.rfc-editor.org/rfc/rfc7489.html#section-11.4)
embedded in the [DMARC RFC (RFC
7489)](https://www.rfc-editor.org/rfc/rfc7489.html). But I was having trouble
wading through all 73 pages of the spec. So, in this case, it wasn't really
necessary. However, for something like the [SMTP service
extensions](https://www.iana.org/assignments/mail-parameters/mail-parameters.xhtml#mail-parameters-2),
it can be much more useful as the service extensions are spread across many
different RFCs.
