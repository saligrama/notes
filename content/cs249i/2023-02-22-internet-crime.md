# Internet crime

## Motives, means, opportunities

* Motives
    - Money *e.g. selling data dumps*
    - Espionage *e.g. Russia FSB hacking Yahoo to spy on domestic and foreign targets*
    - Stalkerware *e.g. Life360, Airtags*
    - Politics/activism *e.g. Sony hack (North Korea)*
    - Clout/reputation/trolling *e.g. Roblox "street cred", Lapsus$*
* Means
    - Social engineering *e.g. SIM swap*
    - Phishing *e.g. DNC attack*
    - Watering hole attacks *e.g. Operation Aurora*
    - Supply chain attacks *e.g. NPM, MSP attacks like SolarWinds*
    - USB drives
    - Card skimming
    - MITM attacks
    - DNS/BGP hijacking
    - Insider threat *e.g. Lapsus$ paying for employee access*
* Opportunities
    - 0days
    - Unpatched, known vulnerabilities
    - Leaked passwords
    - "Darknet" forums
    - Misconfiguration, unsanctioned devices
    - Targeted attacks
    - Poor access controls (often during mergers/acquisitions!)
    - Cryptocurrency (ransomware, smart contract vulns, compute abuse)

## Spam

* Motivation: advertising for both legitimate and illegitimate businesses/products
* Filtering spam is a losing battle

## Phishing

* Phishing: sending emails or communications linking to lookalike system pages to try to get credentials for a service
* Spear-phishing: phishing targeted at a specific person or set of people to try to get a desired level of access to an organization's network
    - Lateral movement: movement within the organization's network post initial access

## Ransomware

* Encrypt an organization's file, hold the data hostage for cryptocurrency ransom: a denial of service on file system access
* Some organizations wised up and started using backups -- so ransomware groups started to dump the data on the public internet instead
* Ransomware as a Service (RaaS): ransomware development groups sell ransomware software to attacker groups who do the targeting
    - Various levels, can even be end-to-end, or other groups participate e.g. initial access brokers
    - Almost always located in Russia
    - RaaS providers take a cut of proceeds
* e.g. Colonial Pipeline attack by Darkside (May 2021)
    - Started with outdated credential to a VPN where credentials had been previously leaked
* Cryptocurrency lead to rise of ransomware, but bitcoin and other cryptocurrencies are not truly anonymous!
    - KYC/AML laws for exchanges
    - Transaction pattern analysis - since wallet addresses are consistent
        - This is what Chainalysis does

## Attacker resources

### Botnets

* Compromised machines controlled by a "command and control" (C2) server that instructs the machines what to do
    - C2 servers have multiple domains reachable by the bots - complicates shutdown procedure
* Structure: "nodes/peers" that can receive incoming connections, "workers" that cannot receive incoming connections (behind NAT/firewall)
    - Commands circulate P2P network by passing commands between nodes; commands get passed to a worker once it reaches out
    - Peer-to-Peer: workers connect to multiple nodes; nodes connect to other nodes
    - When a worker joins botnet it is given a list of IP addresses for nodes to connect to
        - Long set of nodes makes it hards to dismantle the botnet

### Booters

* DDoS as a service for a nominal fee, claiming to offer "network stress-testing services" to appear legitimate
    - Roughly $20/month for unlimited attacks
* Illegal, but most users of the services are minors (e.g. DDoS the person annoying you in Minecraft), and there is no prosecution

### Bulletproof hosting

* Operators that allow/assist hosting malicious content
    - Often used for proxy, C2
* Located in places that do not respect US law enforcement authority e.g. Netherlands, Russia