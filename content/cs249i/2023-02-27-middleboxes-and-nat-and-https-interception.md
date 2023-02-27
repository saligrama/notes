# Middleboxes, NAT, and the End of End-to-End

## End-to-End principle

* Application-specific features should only reside in the communicating end-nodes of the network
    - Intermediary nodes, such as gateways and routers, should not meddle with application-layer components
    - i.e. Application-layer: process-to-process; Transport-layer: host-to-host; internet and link layer below

## Middleboxes

* Def.: a network device that transforms, inspects, and manipulates traffic for purposes other than packet forwarding
    - incl. firewalls, network access translators (NATs), load balancers, and deep packet inspection
    - Extremely prevalent: 2012 SIGCOMM study had average organization's middlebox-to-router ratio around 70%
* Can be good: can be used for monitoring and logging intrusions, dropping malicious packets, balancing web load, etc.
* But: malicious ISP uses too
    - e.g. Verizon advertising header (2014): Verizon injects `X-UIDH` HTTP header that feeds into an API that can be called by ad exchanges; API includes information Verizon has on the user e.g. location, demographics, even credit reports
        - 2016: FCC penalizes Verizon $1.35M for header injection: slap on the wrist, fine likely negligble compared to revenue from scheme

## Carrier-grade NAT (CGNAT)

* End sites (e.g. homes) are assigned private IP addresses; middleboxes translate connections from private to public space
* Tricky: cannot have overlapping subnets on both sides of the NAT
* Three private subnets:
    - `192.168.0.0/16`: small places (houses)
    - `10.0.0.0/8`: enterprises, cloud VPCs
    - `172.16.0.0/12`: leftover, so CGNAT
* Very common: at least 50% of ISPs deploy CGNAT
    - High adoption in mobile networks (90%+)
    - Increasing adoption in residential networks
        - US ISPs slowest as US has the most IP addresses
        - Asia has highest concentration, 10-20% as of 2016
    - Client measurement difficult, need to get a client into the network or hope that someone uses BitTorrent
        - Why BitTorrent? BitTorrent *wants* to be peer-to-peer, but can revert to being a "client" -> which clients are behind *any* sort of NAT are going to be in client-mode
            - Note: this includes people behind just a residential NAT, but can develop heuristics to distinguish (e.g. if your DNS resolver is the same machine as your NAT resolver)
* Disables peer-to-peer communication for e.g. video (need to have all video communication goes through a server)

## HTTPS interception

* Middleboxes and security software now can intercept HTTPS connections to inspect encrypted content
    - Both physical boxes (e.g. Palo Alto Networks) and software solutions (e.g. Avast, AVG)
* How this works: middlebox terminates initiated TLS session, inspects/modifies inner HTTP content, and initiates new outbound TLS session
    - This works b/c middlebox's root certificate is installed on clients, so the middlebox can decrypt traffic while being trusted by the client
    - Note: SSL key pinning on desktop and mobile *browsers* doesn't help, because policy is to ignore key pinning with a locally installed root cert
        - However, mobile *apps* successfully key pin even with root cert presence (which is why I can't really proxy Firebase traffic)
* Measuring interception: websites can potentially detect by identifying mismatch between network layers (e.g. HTTP user agent is Chrome, but TLS has no identifying field or just OpenSSL)
    - Can even use TLS client hello complexity to fingerprint different clients
        - e.g. 98% of intercepted Firefox connections found based on inclusion of cipher options never implemented in Firefox NSS
    - Measurement in ~2017 from Mozilla update servers, Cloudflare, e-Commerce sites: 4-11% of traffic is intercepted
        - 4% is floor from Firefox, since it's not very commonly used in enterprise environments since it doesn't use the OS root store
* Abuse: 
    - Superfish malware on Lenovo devices (2015): installs own root certificate on devices used for interception for advertising purposes
        - But also doesn't perform certificate validation -- creates susceptibility to *any* upstream interception
    - Some countries/ISPs MiTM mobile devices -- mobile ISP sells you a mobile device with its own Android build with a root cert embedded, allowing the ISP to inspect HTTPS packets
        - 15% of mobile traffic in Guatemala subject to interception (2017)
        - 2019: Kazakhstan [tried to MiTM](https://en.wikipedia.org/wiki/Kazakhstan_man-in-the-middle_attack) *all* HTTPS connections in the country -- having end-users install root certs for "security" purposes
            - Browsers blacklisted root cert, forcing government to walk back policy; second attempt in 2020 blocked similarly
* Poor security: significant percentage of middleware advertises ciphers that are decreased security or severely broken
    - 37% of intercepted connections to Mozilla update servers advertised "significantly broken" ciphers (2017)
    - Some even advertise null ciphers (i.e., no encryption!)