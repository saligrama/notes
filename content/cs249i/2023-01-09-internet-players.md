# Intro: internet players

## External BGP: between Autonomous Systems

![eBGP topology](/notes/images/cs249i/2023-01-09-ebgp.jpg)

## Internal BGP (iBGP): within an AS

![iBGP and eBGP topology](/notes/images/cs249i/2023-01-09-ibgp.jpg)

* Set of private ASNs (192.168.0.0/16)
* eBGP: any route received over eBGP: propagate to both iBGP and eBGP peers
* iBGP: any route received over iBGP: propogate to only eBGP peers
* Instead of having a full mesh to take care of this: Route Reflection
    - Central server collects all the routes, decides which to propogate

## Types of routing

* Hot potato: get rid of the packet ASAP (used in practice, greedy)
    - Get it to the next AS that has the shortest path
        - Note: shortest path often means cheapest path
* Cold potato: hold onto packet as long as possible

## IP Transit and Peering

![IP Transit and Peering topology](/notes/images/cs249i/2023-01-09-ip-transit-bgp-peering.jpg)

* IP transit: entity pays an AS to gain access to the broader internet
    - Exchange: $$ for bits and BGP announcment
* Peering: no $$ exchanged, just bits run (b/c both sides get something out of the deal, e.g. two universities)
* Note: Every tier-1 provider peers with every other tier-1 provider
    - This provides full connectivity
    - But: anyone in this deal needs to be a *big* player to participate

![IP transit with Tier-1 and Tier-2 providers](/notes/images/cs249i/2023-01-09-t2-transit.jpg)

* Suppose we're sending a packet from MIT to UChicago
    - Packet won't make it: NTT intermediary will only provide access to its own customers, not to others' customers
    - Would need Sprint and H.E. to be peers
* **This incentivises every Tier-1 to peer with each other**!
* Note: as a non-tier-1, cannot practically peer with everyone due to scale, and large networks won't generally peer with smaller players
* This allows us to define a Tier-1 provider: a network that can access any other network on the internet through peering only
    - Due to well-connectedness, so nobody would charge them for connectivity
    - Little incentive to support new Tier-1s

### Peering policies

* ISPs usualy have one of these peering policies:
    - Open: peer with almost everyone
    - Selective: specific criteria for peers
    - Restrictive: generally does not want to peer
* Peering policies can differ:
    - Usually, ISPs are generally selective (larger usually means more selective)
    - Hurricane Electric will peer with anyone
    - Content providers, e.g. Google, have relatively open peering policies

### Physical peering

* Historically: ISPs would directly connect using physical fiber (direct-circuit peering)
    - Typically, would require a local provider to provide fiber within a metro area
    - Required construction; expensive and slow
    - Sometimes still happens, but only if small number of connections in a single metro area
* Now: carrier hotels / private peerings
    - Specific buildings where ISPs, cloud providers, CDNs, and companies can peer with each other (carrier hotels)
    - Original locations: landing points of underseas cables, but have now increased
        - Most big cities
    - Typically owned by a third party (e.g. Equinix)
        - "Rent" (both physical location and fiber) can be high
        - In Europe: most of these IXPs are run by nonprofits, operate more cooperatively
    - Support private peerings: two ISPs directly connect racks together (cross connect/XC)
    - Provide shared interconnect (switching fabric) between ISPs
        - Allows ISPs to BGP peer with many organizations through a single link (Public Peering)
        - Still need to negotiate BGP peering with others on the exchange
        - Note: more common in Europe than US
    - Multilateral Peering Exchanges: allows those with open policies to all BGP peer with a single entity to both advertise routes and collect routes from others on the exchange
        - Saves ISPs from having to negotiate contracts with thousands of other ISPs
        - Can provide a peering policy; carrier hotel owner will take care of routing and billing for you

### Peering disputes

* If Tier-1s de-peer, then their customers do not have access to the entirety of the internet
    - Causes reputational damage: Cogent has lost customers over this

### Internet flattening

* New changes:
    - Much of content now originates from a few content providers and CDNs
    - These CPs and CDNs now expanded to everywhere in the developed world and colocate with many ASes at IXPs
    - Many many more IXPs than previously, so peering is easier
* Now: tier 1 peerings and internet hierarchy matter less

## Choosing a transit provider

* Transit is a commodity!
* First question: where do you want to send traffic?
    - Choose a tier-1 more local to where the traffic needs to go
* Second question: Bandwidth
    - Committment: I will pay for 3Gbps every month, regardless of how much I use (less than that)
    - Burst: Amount of traffic above committment
        - e.g. 10 Gb Ethernet connection w/ 3Gb commit; amount extra used charged at higher burst rate
        - Charged at 95th percentile rule
            - Every 5 min, capture in/out rate of packets on the route
            - Line up measurements from min to max
            - Figure out where 95th percentile marker is, subtract commit, multiply by burst pricing rate (e.g. $0.10/Mb)
            - Sum of charges billed at EOM
        - Note: allowed to use as much bandwidth as needed for up to 72 min per 24 hours
            - e.g. avoids penalization for unseen amount of packets