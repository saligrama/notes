# Host and Network Addressing

## A brief refresher on IPv4

* IPv4: 32-bit addresses `a.b.c.d` where each quartet is 8 bits
* Hard to refer to an IP space as individual IPs, so come up with CIDR: Class Interdomain Routing
    - e.g. `a.0.0.0/8`: contains `a.` with any suffix
* Minimum size for routing on the public internet is a `/24`
* Address space limited by 32 bits; already ran out of IPv4 addresses
* To get address: client queries DHCP server, which has a pool of addresses to give out, and returns a DHCP lease on an IP address
    - Later: ask for a DHCP renewal to keep using the IP address (without renewal, timeout and IP goes back in the pool)

## IPv6

* 128-bit addresses for a larger possible address space
* But also: cleaned up a bunch of other legacy artifacts from IPv4; lots of unnecessary fields gone
* Addresses have two halves (so smallest assigned IP is a /64):
    - Network part is always a /64, containing:
        - Registry (/23)
        - ISP prefix (/32)
        - Site prefix (/48), smallest prefix allowed to be advertised
        - Subnet prefix (/64)
    - Interface ID is a /64
* IPv6 has no ARP; has DHCP, but is different:
    - No broadcast
    - Multicast address for all local routes
    - Send out router discovery request (ICMP), dest: multicast for local routers
    - Router does not send back client IP; sends back a bit saying managed IP address space or unmanaged
        - Unmanaged IP case: responsible for finding own IP address
            - Pick random value (neighbor discovery request) - used in practice, as it's easier than the other following option
            - Or: MAC addresses are supposedly unique, so use `<24 bits of MAC>::fffe:<24 bits of MAC>` = 64 bits
                - However, this allows tracking of a device *across networks*!
                - So, keep a global counter of the number of times we needed to come up with an IP address, IP suffix is: `MD5(MAC || ctr++)`
* Numbers of hits of Google: up to 35-40% adoption of IPv6
    - Mostly on mobile phones
    - Mobile and cloud providers love v6!