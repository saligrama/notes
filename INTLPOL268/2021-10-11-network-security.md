# Network Security, Part I

## How does the internet work?

* Packet Switching
    - Break up internet communications messages into packages, release them onto the network, each node in the network forwards it to the next node until reaching the host which reassembles it into the correct order
* Two universally used protocols:
    - IP - Internet Protocol
    - BGP - Border Gateway Protocol
    - Internal networks can be different (Comcast uses DOCSYS, AT&T uses LTE and 5G) but the networks themselves talk IP/BGP to each other

## Web layers

Notes:
* Only 4 layers: OSI Model is irrelevant to how networks work in 2021!
* Layers "go inside" each other, not top-to-bottom

### Physical/Link layer
* Moves actual data between two systems
* Only provides for ability to comunicate one hop
* Link and physical are generally tied together to make things "just work"
* Example: Ethernet

### Internet layer
* Provides for addresses that work across the world
* Can be carried on many different physical layers
* Is best-effort, no guarantee it will arrive
* All IP packets arrive in the same "inbox" at destination

IPv4: Can have 2^32 ~= 4bn addresses
* But we can share IP addresses!
    - Special reserved part of address space (e.g. 192.168.\*.\*, 10.100.\*.\*)
    - Router/firewall translates into one public address

IPv6: Can have 2^128 addresses

### Transport layer
* Provides reliability
* Allows for multiple services
* Allows for multiplexing
* Example: port numbers for different application

### Application
* Basic language: HTTP
* Plus stuff on top of it: HTML/CSS/JS/etc