# Last-Mile Internet Access

Def.: The final leg between the telecommunications network (cable, Internet, etc) and the end user (home, university, cellphone, etc)
* Tends to be the *most* bandwidth-constrained, as it costs lots of money to invest in infrastructure to *everywhere*

## History of last-mile

* Early 90s: 56k dialup was the way to connect
    - Leveraged existing telephone network to connect homes to the internet
    - However: *very* slow, and restricted by modems themselves - 56kbps was *theoretical* maximum, in reality was ~30-40kbps
    - Also: Could not use phone and internet at the same time
* Late 90s: Digital Subscriber Line (DSL)
    - Different frequency channel (so different hardware) - allows people to use phone and internet at the same time
    - Early on, up to 256k speeds
    - Much of the world *still* uses DSL
* Early 00s: cable internet
    - Core idea: use cable television network for internet; typically copper cables or copper/fiber hybrid
    - 80% of Americans use cable to connect to "broadband" (defined by FCC as stable, 25mbps DL, 3mbps UL, but term has been diluted by cable companies)
        - Even still, 21M Americans don't have broadband access; 1/2 of global population does not either
    - Caused cable TV companies to become ISPs
* 2010s: Fiber-to-the-Premises
    - Available in densely populated areas in developed regions
    - Major US, European players, e.g. Google, Verizon, etc; smaller players are getting in as well
    - However, very costly: $27k per mile of fiber
* Main areas of investment:
    - Wired (cables)
        - Copper, coax at last mile, but could be fiber in developed regions
        - Fiber in backhaul/backbone
    - Wireless
        - Satellite
        - Cell towers
        - New moonshots

## Telecom topology

* National backbone network (core): submarine cables that terminate at points of presence (PoPs)
* Middle-mile network (backhaul): satellite, fiber, or cellular connections terminating at base station tower or local PoP
* Last-mile network (access): wireless/wired connection to home or cellular divice

### "Dig-Once" policies

* When doing other infra. projects (e.g. construction, sewage), build in broadband connection you need instead of separately
    - Estimated to save $126B of $140B of nationwide fiber cost
    - However: only 16 states have implemented policies - CA included
    - Nationwide legislation stonewalled, due to lobbying from larger telco providers as the policies would give smaller regional providers low-cost access to building fiber

### Mobile networks

* Exploded in popularity in the last 20 years as cheaper alternative to broadband access than fiber
* 4G networks offer broadband speed, but expensive to build: ~$200k per cell tower
* Generations (every generation, ~10x increase in bandwidth):
    - 1G: basic voice calling services
    - 2G: Voice calls, text messages, limited browsing
    - 3G: Broadband, video conferencing, GPS
        - 4G LTE: "Long-Term Evolution" transition phase to 4G, but not actually 4G
    - 4G: high-speed apps, mobile TV, wearable devices
    - 5G: HD streaming, IoT, autonomous vehicles

#### 5G networks

* More than just increased bandwidth compared to 4G; 5G networks are expected to eventually support:
    - Enhanced mobile broadband: extreme data rates, extreme capacity
    - Mission-critical control: ultra-low density, ultra-high availability, extremely mobility
    - Massive IoT: devices with ultra-low energy, ultra-high density
* Improvements in how data is multiplexed onto radio spectrum
* New frequency bands: Higher frequency generally means faster data rates, but less coverage and range

#### Cloudification of access

* Cellular networks have been historically extremely opaque: proprietary hardware, slow to innovate
* Industry is re-architecting access network using principles of SDN: run 5G network functions in the cloud rather than on purpose-built applications
    - Win-win: network operators get faster innovation and CAPEX savings; cloud providers get low-latency connectivity to end-users on their devices

### Satellite networks

* Works through connection of satellites (often privately operated) that are flying in geostationary orbit (35km above)
* Connections are SLOW
* Costly to build, deploy, and operate, and thus costly to purchase
    - 25mbps from Comcast: $30/mo (in theory)
    - 25mbps from Viasat: $150/mo

### Internet encroachment on TV spectrum

* In ~2007: researchers started to look into using TV (lower-band) channels for long-range communication for Internet connectivity
    - Lower band: can travel greater distances and move over more obstacles; cheaper to deploy and requires lower base stations
* Idea: use whitespaces (gaps in TV-allocated frequencies)
    - Protests from TV companies, until FCC cleared the concerns in 2019