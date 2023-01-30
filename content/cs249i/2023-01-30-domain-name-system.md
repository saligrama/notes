# Domain Name System (DNS)

An aside: "Hourglass Model of the Internet"

1. Email/general Application layer
2. DNS/TLS
3. TCP/UDP - up till here, the internet we interact with
4. IP/BGP (narrowest part, i.e., everything needs to go through here)
5. Link layer, Ethernet, Cellular

If a layer's security is compromised, everything above it could be compromised.

## Motivation for DNS

* Communication on the internet is via IP, but it's hard to remember IP addresses!
* But: easier to remember names, so map names to IP addresses (e.g. `cs249i.stanford.edu` -> `185.199.108.153`)
* Originally, a centralized solution - a `hosts.txt` file that has a mapping for all hosts:
    - Organization: host -> IP address
    - Store in `/etc/hosts` on system
    - Historically, Stanford Research Institute (SRI) kept main copy, a single place to update records by downloading them periodically
    - However: fragile, hard to scale and keep in sync
* Now, a decentralized solution:
    - Root nameserver has a mapping: organization -> name of organization nameserver
    - Organization nameserver has a mapping: host -> IP address

## DNS hierarchical namespace

1. DNS root `.`, owned by ICANN (prev. US Dept of Commerce, now independent nonprofit)
2. TLDs; e.g. `com.`, `edu.`, `us.`, held by TLD authoritative nameservers (e.g. Educause, Verisign), authority delegated from ICANN
3. Domain: e.g. `stanford.edu.`, Stanford authoritative nameservers (authority delegated from Educause to Stanford)
4. Subdomain: e.g. `cs.stanford.edu.`, authority delegated from domain owner

## DNS resolution

1. Client host: has Client Stub Resolver, asks recursive resolver to resolve a domain (e.g. `cs.stanford.edu`)
2. Recursive resolver only has location for Root Authoritative NS, queries for `cs.stanford.edu`
    - Root NS: responds to ask `.edu` authoritative NS, with its location
3. Recursive resolver asks `.edu` auth NS, queries for `cs.stanford.edu`
    - `.edu` auth NS: responds to ask `ns1.stanford.edu` and `ns2.stanford.edu`, with their locations
4. Recursive resolver asks `ns1.stanford.edu` for `cs.stanford.edu`
    - `ns1.stanford.edu`: responds `171.64.64.64`
5. Recursive resolver responds `171.64.64.64` to client stub resolver

Note: recursive resolvers will not tell you the IP addresses of nameservers, but authoritative nameservers will

## DNS query structure

Queries happen over UDP, with the following fields:

1. UDP header
2. DNS header
    - ID
    - QR
    - Opcode
    - AA
    - TC
    - RD
    - RA
    - Z
    - Rcode
        - 0: `NOERROR` (success)
        - 2: `SERVFAIL` (server failed to complete request)
        - 3: `NXDOMAIN` (domain name does not exist)
        - 4: `NOTIMP` (function not implemented)
        - 5: `REFUSED` (server refused to answer queries)
    - Query count
    - Answer count
    - NS count
    - Additional record count
3. Question section
    - Query name (`QNAME`): domain to resolve
    - Query class (`QCLASS`)
        - `IN`: internet
        - `CHAOS`: used for debugging
    - Query type (`QTYPE`)
        - `NS`: authoritative nameserver for domain
        - `A`: IPv4 address
        - `AAAA`: IPv6 address
        - `MX`: mail exchange records
4. Answer section
    - Name
    - Type
    - Class
    - TTL (how long to cache answer)
    - RDLength
    - RDATA
5. Authority section
6. Additional info section

### Caching

* Why cache DNS responses?
    - Reduces load
    - Improves latency
    - Reuse results of previous queries
* How long to cache? Governed by Time to Live (TTL)

## DNS failures

* Misconfigurations: typos, misconfigured auth NS
* Hardware/network failures: unreachable nameservers
* Large traffic volumes; DoS attacks

## DNS reliability

* UDP used, because faster than TCP (no need to do the whole handshake for a short single-use query)
* Reliability through replication
    - Two auth NS per domain
    - Multiple root NS's, TLD authoritative NS's

## DNS confidentiality

* Everyone on path can see your DNS queries!
* DNS-over-TLS/DNS-over-HTTPS solves this, by directly routing queries to DNS providers like Google and Cloudflare
    - This made ISPs livid, because they were selling data about DNS queries they could collect in the clear
    - However, ISPs can now try to pretend to be 8.8.8.8 - used for censorship