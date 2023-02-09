# The Modern Web

## A brief evolution of the web

* Earliest websites provided static content with little additional media
* Over time - websites grew to include many more resources, e.g.
    - More pages
    - Static assets: images, logos
    - Dynamic content (JS, AJAX)
* Modern websites are incredibly complex; rely on often hundreds of resources to properly function

## HTTP/0.9 (1991)

* Request: single line command, only supported retrieving HTML content
    - `GET /index.html`
* Response: file data itself
* Built on top of TCP, for reliable transport of data; connection closed after every request

## HTTP/1.0 (1996)

* Goals: generic, **stateless**, object-oriented protocol which can be used for tasks, such as nameservers and distributed object management systems (RFC 1945)
* Added versioning, new methods e.g. `POST`, `HEAD`, `PUT`, `DELETE`, supported many content types, included headers for requests and responses
* But: still connections closed after single requests, which made things inefficient and slow when sites had more resources
   - Could not collocate multiple sites at same IP address

## HTTP/1.1 (1997)

* Fixed many HTTP/1.0 problems:
    - Host header allowing collocation of multiple sites at same IP address
    - Allowed for persistent connections: via browsers opening up to 6 TCP connections per host, plus 4 external TCP connections at a time
        - Chunked responses: allowing servers to break up responses for large objects into independent TCP transfers - allowed streaming
        - Request pipelining: can send a bunch of requests on the same TCP connection without waiting for a response
            - But: head-of-line blocking means that subsequent resources on a shared connection need to wait for first request to be serviced before they could be served

## Evolution 1997-2015

### Context

* Modern websites exploded with dynamic content; increased reliance on web resources for online experiences
    - In 2011: median number of requests per modern webpage was 40
* Internet speeds and infrastructure improved; networks matured quite a lot
    - But much more load due to new people onboarded online
* HTTP/1.1 was not enough to meet demands of growing web

### Google SPDY

* Google controlled both browser and server; developed SPDY to try to reduce website page load times
* Translation layer between HTTP clients and servers; sat in front of HTTP on both ends; implemented by Chrome and Firefox
* Formed foundation for HTTP/2

## HTTP/2 (2015)

Design goals:

1. Eliminate HoL blocking by multiplexing HTTP requests over a single TCP connections
    - HTTP/2 uses a single TCP conection for any number of HTTP requests and responses
        - Everything is logically separated by 4-byte `stream_id` field
    - If server takes signficant amount of time for one request (incl. first one), other requests can still be completed while waiting for that one
2. Give servers more agency (e.g. allow them to push content over persistent connections)
    - Server Push: enables server to send data to client; even if client hasn't requested it yet
        - Used for static content that server knows client will need to render the page
        - Implemented using a `PUSH_PROMISE` frame on a new stream, which asks to reserve an HTTP/2 stream for pushing additional data to the client
            - Clients can reject push sending `RST_STREAM` frame, e.g. because resource is already in cache or client is too busy
            - However, this may create traffic fingerprinting concerns
3. Reduce unnecessary duplicate bytes sent over the wire, (e.g. static headers)
    - In HTTP/1.x, headers always sent as plaintext, despite the fact that many are static and unchanging
        - Application data already compressed (e.g. `Content-Encoding: gzip`), but not done for headers at protocol level
    - HTTP/2 uses HPACK compression algorithm:
        - Compress header data (Huffman coding)
        - Keep shared compression table on client and server; dynamically updated with new requests on every request/response
            - Encodes static table with 61 entries for most common HTTP headers into every client and server - allows sending encoded index value rather than header itself
            - Every subsequent request is dynamically encoded and added to shared table

Adoption:

* Client: near 100%
* Server: around 60% as of mid-2021

Speedup:

* Generally, we see performance benefits (e.g. 25-50% for Financial Times)
* However, with poor network conditions, a single HTTP/2 connection performs worse than 6 parallel HTTP/1.1 connections
    - Why? HTTP/2 solves HTTP-level HoL problems, but introduces blocking problem at TCP-level with packet drops

## QUIC (2021)

* Idea: combine TCP and TLS into a new transport layer in user-space, underlying HTTP stack, built on UDP
* Design goals:
    - New, reliable transport layer
    - Easily deployable and evolvable: userspace; doesn't require updating routers
    - Secure and encrypted by default: build encryption, integrity checks, authentication into transport layer itself
        - Can put many transport-layer headers (including packet ordering) into the encrypted section
    - Reduce unnecessary delays caused by strict layering: TLS handshake delays; HoL blocking (HTTP, TCP)
        - e.g. UDP allows certain packets to be dropped without anyone caring, e.g. video frames
        - UDP also allows persistence of session beyond traditional network boundaries (i.e., client IP address changes doesn't mean the session dies)
            - Uses session IDs to verify connection even through network change
* Deployment:
    - Ratified by IETF in May 2021 (RFC 9000)
    - Support existed in Chrome for a while, but now available in Firefox
    - ~6% of websites use QUIC, including all of Google and ~75% of Facebook
    - Some ISPs have 20%+ of packets over QUIC

### Establishing a QUIC connection

* First time client wants to communicate with server: "inchoate client hello" sent in cleartext, initiates `REJ` (reject) from server
    - Server sends back cert chain (for server authentication) and other server metadata
* Client will then use server info to send "complete client hello", then immediately starts sending encrypted data
* Client caches server details based on origin, so client uses cached server data to send encrypted messages moving forward (0-RTT protocol)