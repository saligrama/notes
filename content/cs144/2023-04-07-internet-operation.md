# How the internet works

Base level: protocol stack, e.g.

```
(application)---datagram socket--->(user datagram protocol impl)---payload--->(internet datagram protocol impl)
--->(IP)--->(UDP)---datagram socket--->(application server)
```

Note not all of these are visible, because of encapsulation!

## What users and applications want

* Reliable retrieval of a short piece of data
    - e.g. IP address lookup for `saligrama.io`
* Reliable action
    - Text of 7th message is "Fire a torpedo"
* Reliable byte stream
    - Sequence of bytes in each direction delivered in order, correctly
* Reliable delivery of a large file (FTP, SMTP, HTTP)
* Reliable remote procedure call (e.g., HTTP/1, HTTP/2, HTTP/3, gRPC, Thrift)

* "Request-response abstraction" -- build on top of user datagrams
    - Idempotency of requests: some requests always create the same response (e.g. HTTP GET)

## Dynamic Host Configuration Protocol (DHCP)

* Over UDP port 67
* For a host to request an IPv4 address

## Domain Name System (DNS)

See CS249I notes.