# Internet Censorship and Circumvention

* Working definition: censorship occurs when an entity blocks a user request to a resource
* Before: only a few governments had access; but now, commercialization of these practices

## How do censors restrict access?

1. Internet shutdowns
    - e.g. state-sponsored ISP blocks all ingress/egress from country; typically used in times of political turmoil like protests
    - High political and social cost
    - But becomming more common: 2020 saw 155 political internet shutdowns in 29 countries
2. Throttling
    - Slow down internet content, e.g. Google slows down twitter
3. Content takedown e.g. DMCA
    - Some legitimate e.g. Bangladesh getting Google Play Store to take down malware app using country's insignia
4. Content blocking

## Content blocking

### DNS manipulation

* ISP usually provides DNS resolver, usually unencrypted
* When client tries to connect to a server, DNS request made; country ISP provides an ISP like localhost to block it
    - Or redirect to some site showing a blocked message

### IP blocking

* Client tries to send requested server a TCP handshake
* But country censor looks for that IP egress, and then drops or sends a RST packet to the client
    - Gives client the impression that the server is unavailable at the moment
* More common than DNS blocking
* Now less likely as IP addresses tend to mean services rather than servers (i.e. with virtual hosts)

### Application-layer blocking

* After TCP handshake, try to send HTTP(s) request to the server
* Censor could do blocking here, or if there's a cert decrypt your connection and send you back bad information
* Could happen with any application-layer protocol

## Measuring internet censorship

* Motivation:
    - Information controls harm citizens
    - Spreads beyond large countries
    - Frequently opaque in topic and technique
    - Want to know where, how, when, what is being censored
* Measurements help to:
    - Increase transparency and accountability
    - Improve technical defenses
    - Inform users and policy: users aware of censorship take actions enhancing internet freedom
        - e.g. Turkey spray-painted 8.8.8.8 DNS in 2014

### Challenges

1. Censorship methods are varied (see above)
2. Censorship varies around the world: geographical and network variance
3. Censorship varies across time: cat and mouse game

### Methods

* Open Observatory of Network Interference (OONI) probe: agent that runs on volunteers' personal computers
    - Client will send a request to server of interest through censor, but also through control relay
    - Limitations:
        - Scale
        - Coverage
        - Continuity
        - Cost
        - Ethics
            - When is it safe to use volunteers' devices?
            - What population of users are affected?
            - What risks do volunteers occur, and do benefits balance these risks?
* Spooky scans (Ensafi et al. PAM 2014): using TCP/IP side channels to detect if clients and servers can communicate
    - Can detect "no blocking"; "server-to-client blocking", "client-to-server blocking" by using IP spoofing from a measurement to measure behavior of  client/server communication through censor
    - This can uncover IP blocking
* Censored Planet Obeservatory
    - Many different techniques
    - Detect blocking on TCP/IP, HTTP, HTTPS, Echo, Discard
    - Impact: study of 2019 Kazakhstan national TLS interception that led to Firefox and Chrome pushing updates to block usage of Kazakh root CA certificate

## Circumventing internet censorship

1. Proxying requests through "safe" servers
    - Censor only sees that user is trying to reach benign proxy server, not a blocked service
    - Very easy to detect; becomes a cat-and-mouse game for censors
2. Imitate non-censored protocols
    - Skypemorph (2012): disguise Tor traffic as skype video traffic using fake skype servers and clients
    - But: also easy for censors to detect using network features (e.g. anomalous-looking UDP packets as a key) - Houmansadr et al. 2013
3. Refraction networking
    - Bring proxy behavior into the core of the network via cooperation with friendly ISPs
    - When user requests a blocked site, client software requests a reachable site hosted by ISP partner network; censor allows request to pass through; ISP partner refracts request to the blocked site
    - Deployed in the wild: VanderSloot et al. PET 2020