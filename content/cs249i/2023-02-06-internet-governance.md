# Internet Governance

* Motivation: little central coordination between ISPs; everyone makes their own commercial decisions
    - But: other aspects need centralized organization, e.g.
        - DNS and name registration
        - IP and MAC address allocation
        - WHOIS
        - Port numbers
        - Protocol identifiers

## Internet Assigned Numbers Authority (IANA)

* Originally: Jon Postel as "czar" of port numbers (RFC 322)
* Postel's efforts eventually became Internet Assigned Numbers Authority (IANA)
    - Became official in ~1988, when DARPA provided funding to USC to maintain functions
    - In 1988, IANA control transferred to ICANN (see DNS notes)
* ICANN contracts out IANA's operations to Public Technical Identifiers (PTI) to maintain infrastructure

## More on ICANN

### Domain's and gTLDs

* Originally 7 gTLDs (`com`, `org`, `net`, `int`, `edu`, `gov`, `mil`)
    - `net` originally used for internet infra operators
    - `arpa` used for reverse DNS pointer lookups
    - Generic restricted: `biz`, `name`, `pro` for specific purposes
    - Sponsored: `aero`, `asia`, `jobs`, `travel` for specific industries
* In 2011, ICANN began selling gTLDs for $185,000

#### Verisign and `.com` TLDs

* IANA/ICANN doesn't run TLDs themselves -- they approve and delegate control by issuing NS records that point to other providers
* Historically: SRI and then Network Solutions controlled `.com` TLD
* In 2000, Verisign acquired Network Solutions and became `.com`, `.net`, `.org` registry
    - Continues to be provider under ICANN regulation and contract
    - ICANN sets terms such as maximum that Verisign can charge registrars per domain ($8.39 since 2021)

#### The `.org` dispute

* In 2003, Verisign transferred control of the `.org` TLD to the Internet Society (ISOC)
    - Widely understood the reason was to financially support ISOC
    - In 2018, PIR (ISOC subsidiary) revenue from `.org` was over $92M
    - Technically, PIR contracts work out to Afilias, who runs many ccTLDs
* In 2018, ISOC tried to sell PIR to a private equity firm
    - However, transfer required ICANN's approval
    - Significant external concern, incl. from California AG's office
    - ICANN ultimately blocked the transfer

## Regional Internet Registries (RIRs) and the exhaustion of IPv4

* 5 RIRs:
    - AFRINIC (Africa)
    - APNIC (Asia-Pacific)
    - ARIN (North America)
    - LACNIC (Latin America)
    - RIPE NCC (Europe, former Soviet Bloc, Middle East)
* In mid-2010s, IANA and subsequently RIRs ran out of all unallocated IPv4 blocks
    - Last was AFRINIC in Sep 2017
    - Partially due to bad early IPv4 allocation decisions, e.g.
        - Stanford (two `/8`s, much returned by 2000)
        - MIT (sold half of `18.0.0.0/8` in 2017)
* IP markets: permissible to transfer ownership (i.e. sell) IP blocks larger than `/24`
    - Transfers approved by RIRs: ensures that desination organization has good reason for number of IPs purchased
    - Prices increase; as of early 2022 ~$30.00 per IPv4 addresses
    - Example price on `auctions.ipv4.global`: `/24` for $14,080, `/21` for $112,640
    - Attracts investors to squat IP addresses due to prices going up
* In some regions, ISPs have ran out of IPv4 addresses to assign to end users
    - Indicated by very short DHCP leases, or some ISPs (e.g., mobile) are entirely NATted, so *no* end users get a public IPv4 address