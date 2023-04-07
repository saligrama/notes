# Datagrams, Encapsulation, and Multiplexing

Key terminology: **service interface/abstraction** -- the contract that a computer system is expected to fulfill

Entities of a network:

* Hosts (endpoints)
* Routers
* Switches

## Datagram

Fundamental unit of a network: a datagram ("postcard") with fields

* To: some entity (a name with meaning in some context)
* Payload

such that the **network has a contractual agreement to make a "best effort"** at delivering the datagram to the entity.

Outcomes during "best effort delivery":

* Recipient actually receives it
* Recipient receives it after a *long* delay
* Recipient never receives it
* Recipient receives multiple copies
* Recipient receives it after another datagram sent later
* Someone else could intercept it (and maybe change contents in transit)

Internet fulfills contract of where to forward packets to ultimately -- therefore a datagram must contain the final address.

Standardized: Internet Datagram Format (STD 5 / RFC 91)

Round trip time: time between message sent and reply received

**Distinguishing feature**: independent, contains enough info to be routed to *final* destination

## Encapsulation

* Once we have a contract to deliver a message with a payload, the payload can be anything arbitrary
    - Protocol captured by the header doesn't need to care about the payload!
* Once a packet is received by some entity speaking a protocol, its payload is *deencapsulated*.
* Allows us to make the internet perform a lot of tasks -- by encapsulating next-layer protocols within the payload of the previous layer.

## Multiplexing

* Sharing of a resource by runtime dispatch on an *identifier*
* How we can send not just to a host, but to an ordinary application
    - Internet datagram is host-to-host
    - But user datagram is instead addressed to a port number / process (protocol 17)
        - Payload includes message
        - Protocol field included