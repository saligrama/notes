# Modern Routing Practices

* A note about private IPs: we can actually ping a private IP from a public IP, as long as there is a route to it
    - The only thing special about private IPs is that ISPs will not allow you to advertise the prefix onto the internet

## How a router works

* Context: each router is connected to some finite number of other routers
* RIB: Routing Information Base
    - Inputs:
        - Static routes (configured in text files)
        - iBGP
        - eBGP
        - OSPF
    - Output to Control Plane (software, in memory)
    - Routes are organized by prefix
        - e.g. `103.127.54.0/24`
        - Can have a different route via each of the input types; there is a priority ordering between source for a route
    - Radix tree of paths to a prefix
        - Next hop router chosen by lookup prefix in the radix tree
    - ASNs: an array `[ASNa, ASNb, ..., ASN_origin]`
        - Origin AS should be consistent; only one AS *should* be advertising the prefix
        - Can add your own AS multiple times in the path to inflate length of the advertised path; this biases against a packet being sent down that path
* FIB: Forwarding Information Base
    - Note: In Proj1 the FIB is the Linux kernel
    - Hardware FIB: RIB has information for next hop; this is used by FIB to take a prefix and send the packet out to a physical port corresponding to the next hop

## Multiple ASes advertising the same prefix

* e.g. AS 1 advertises 1.2.3.4/24, AS 2 advertising 1.2.3.4/24
    - If the advertising is happening in different geographical areas (e.g. Europe vs US)
    - e.g. AS 1 advertises on path [5, 4, 2, 1], AS 2 advertises on path [6, 9, 8, 2, 1]
* Anycast: allowed to send packet through anywhere - effectively sends through shortest path

## BGP Security

### BGP Hijacking

* No built-in security in BGP - any AS can advertise any prefix
    - Others will choose shortest path, regardless of whether it's a correct path
* e.g. Russian provider in 2018 advertised IP prefixes that contained Route53 AWS DNS servers
    - Hijacked queries for crytpocurrency wallet sites, directing HTTP requests to imposter sites, allowing them to steal $152k
    - Can go claim a cert from Let's Encrypt as now advertising the false prefix, so HTTPS does not help
* ISP-provided protections
    - For an end customer, ISPs should only accept that end customer's IP address block; any other prefix advertised from that customer should be dropped
    - Easy for cusotmers, but hard to understand for ISPs what to filter from other ISPs

### Resource Public Key Infrastructure (RPKI)

* PKI that communicates who owns IP prefixes and the AS number that can originate, in an object known as a Route Origin Authorization (ROA)
* Each RIR (Internet Registry) posts public keys and act as trust anchors

## Multiprotocol Label Switching (MPLS)

* Routing technique where path through network is determined at ingress
* Short (layer 2.5) label tacked onto front of packet
* Routers use tag to very quickly forward to next router; egress strips label
* Effectively does L2 routing - avoids expensive L3 IP longest prefix match at each hop

### Remote Peering

* Do not need to physically have fiber directly between two entities to peer - just need one entity to create a VLAN including the other entitity
* This is effectively done using MPLS: just labeling packets where they need to go