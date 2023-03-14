# Content Delivery

Why study content delivery?

* 71% of Internet traffic traverses a Content Delivery Network (CDN)
    - How and *why* the modern internet works
    - Every big company does this
* Creating CDN's is an interesting systems/network problems - hot research topic at systems and networking conferences

## A world without content delivery strategies

* Unicast: suppose we have a server `1.2.3.4` in California and a client in Spain
    - To setup the connection: just to set up TCP, takes 100ms each for SYN, ACK, and SYN/ACK = 300 ms!
    - Then for TLS handshake: 100 ms * 4
    - So total: 700 ms, with no content!
    - Add a tiny static HTML page: 900 ms **round trip time** (RTT) incl content
    - This is too slow! Google found that for each 400ms in load time, decrease in searches by 0.74% (which at Google scale is tens of millions)
* Satellites make this problem even worse: SpaceX/Starlink centered around decreasing satellite altitude just to decrease RTT

## A world with content delivery strategies

* Def. Edge / Point of Presence (POP): a server located relatively near the client
* e.g. put an edge in the UK for connection proxying; suppose it takes 10ms to get a packet to the UK from Spain
    - TCP handshake from client to edge takes 30ms
    - Client does not need to wait for the edge to initiate a TCP/TLS connection with the server: already ambiently have the connection, kept alive
    - TLS handshake from client to edge takes 40ms
    - Content is the only thing needed to go across the ocean, takes 2 * 100ms
    - Total: 280ms RTT; >3x savings

## Content delivery optimizations

* HTTPS/2 multiplexing: allows data requests to be sent in parallel; responses can be sent as soon as they are ready without blocking
* TLS session resumption: can resume an encrypted session without a session ID, without needing to do a full TLS handshake again

## Content Delivery Networks (CDNs)

* Motivation:
    - A single content server is still far away from the edge
    - Also vulnerable: single point of service failure
    - Want to move content closer, with many content servers, to have an edge close to the client
* Options:
    - Cache popular static content in the edge
    - Make edge a replicated content server (better for dynamic content)
    - Can combine both

### Strategies for locating edge servers

* *Scattered* strategy: prioritize physical distance to client, with many low/medium capacitity PoPs
    - Advantages: easy to deploy, less distance to cover causes less latency, effective in low-connectivity reasons
    - Disadvantages: tough to maintain/update
* *Consolidated* strategy: prioritize fewer, but more powerful PoPs (usually collocated at data centers and IXPs)
    - Advantages: serve/cache more content; better connected to next hop; provides DDoS mitigation; easier to maintain/update
    - Disadvantages: tough to deploy new PoP; less effective in low-connectivity regions
* In the real world: use both! (Amazon Cloudfront, Akamai, Netflix Open Connect)
    - Netflix doesn't run its own network; rather places content on ISPs networks in exchange for a promise that it will pre-position content outside peak hours to save bandwidth

### Client choice of PoPs

1. DNS routing, "Map the Internet", Unicast approach
    - CDN's DNS resolver uses client resolver IP to return edge/PoP IP closest to the client
    - Every edge/PoP is assigned its own unique IP address
    - Requires centralization of authoritative DNS resolver for the CDN
    - Upon failure of an edge/PoP, DNS must detect and reroute
    - Advantages: CDN has direct control of edge is chosen; real-time rerouting
    - Disadvantages:
        - Extra infra/ops (e.g. health monitoring of edges/PoPs) required
        - Availability requires short TTLs creating a lot of DNS lookups
        - DNS doesn't know client's real location (since the resolver location is not always the client location, e.g. when using `8.8.8.8`)
2. Anycast routing approach
    - PoPs share IP addresses
    - Relies on BGP to route client to the closest PoP
    - Biggest user is Cloudflare
    - Advantages: clients choose edge location so CDNs don't need to guess; BGP makes this naturally reactive to failures
    - Disadvantages:
        - Manipulating traffic slow due to reliance on BGP propagation
        - BGP route flaps: TCP `SYN` and `ACK` can theoretically get directed to different servers
            - Route flap damping (which penalizes constant route changing) takes care of this
        - Have to predict which PoPs will likely receive the most traffic in advantage, since can't easily change the hardware/infrastructure
            - Can create overload/underload if predictions are wrong
3. Multicast
    - Saves bandwidth between host and router
    - Planned usage in satellites for CDN to free up fiber (Dowhuszko et al. 2020)

### FastRoute: handling anycast overload (Flavel et al. NSDI 2015)

* Systems goal: reroute anycast traffic when edge gets overloaded
    - Want this to be as simple as anycast, but just enough control to reroute
* Consider anycast "layers": Datacenter edges (IP A), Backbone/IXP edges (IP B), Reach edges (IP C)
    - Tiers can get routed to that tier or any higher tiers; IP A gets guaranteed a datacenter edge
    - DNS chooses which IP address layer to hand out to the client, then uses anycast to route to that group of nodes
        - How? DNS servers located in same location as edges; they talk to each other
        - If DNS server detects its neighboring edge is being overloaded, it starts handing out IP addresses for next layer
        - This is still decentralized!

### DDoS mitigation via Anycast CDNs

* Background: DDoS (Distributed Denial of Service) attacks usually coordinated by attacker's control server, that tells a botnet to initiate an attack and the botnet targets the single server with attack traffic
* Anycast CDNs will route botnet traffic around to different edge servers, reducing load on any individual edge server
