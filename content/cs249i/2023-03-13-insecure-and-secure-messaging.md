# Insecure and Secure Messaging

## Email

### Simple Mail Transfer Protocol (SMTP)

* First introduced in 1982, extensions in 2008 (such as STARTTLS, SPF, DKIM, DMARC)
* Messages are ASCII cleartext -- no security:
    - Confidentiality: no protection against eavesdropping; anyone on-path could read
    - Integrity: nothing prevents active attacker from modifying messages in transit, or spoofing emails as you
    - Availability: little guarantee of uptime or availability of email data

### Legacy email delivery flow

1. Alice `alice@uiowa.edu` sends email to `bob@stanford.edu` via a SMTP submission to a server such as `smtp.uiowa.edu`
2. SMTP server makes an `MX` record DNS lookup on `stanford.edu`, and gets back something like `smtp.stanford.edu`
3. `smtp.uiowa.edu` makes an SMTP relay to `smtp.stanford.edu`, which sends the email to the receiver e.g. `pop3.stanford.edu`
4. Bob checks and receives email from `pop3.stanford.edu`

Security:

* TLS between Alice and `smtp.uiowa.edu`; TLS between Bob and `pop3.stanford.edu`

### STARTTLS protocol (RFC3207, 2002)

* Enables sending server to start an encrypted TLS session when delivering mail; messages transferred over encrypted session
* Flow:
    1. S1: TCP handshake
    2. S2: `220 ready`
    3. S1: `EHLO`
    4. S2: `250 STARTTLS`
    5. S1: `STARTTLS`
    6. S2: `220 GO HEAD`
    7. TLS negotiation
    8. Encrypted email
    - Note steps 1-6 are all in cleartext!
* Note: SMTP servers according to RFC3207 cannot require use of STARTTLS session in order to deliver mail (for interoperability purposes)
    - Therefore, if STARTTLS handshake fails, mail is delivered over cleartext (opportunistic use)
    - *Many* servers do not support STARTTLS
    - STARTTLS tripping: MITM can simply delete or munge a STARTTLS message to induce a server to send mail in cleartext
        - In the wild: 96% of emails delivered to Tunisia, 26% in Iraq are delivered in cleartext
        - However, not necessarily malicious, as some middleboxes advertise STARTTLS stripping as a feature to prevent attacks and catch spam
* Additionally: unclear what name to go for certificate validation
    - `MX` record e.g. `smtp.gmail.com`: no real security added (plus DNS MITM returns bad `MX` record)
    - Domain e.g. `gmail.com`: doesn't work for cloud providers (because TLS certificate validation needs to happen on ports 80/443, since there is no way to link a X.509 certificate to a protocol)
* Usage: jump from ~25% of Gmail inbound/50% Gmail outbound in 2013, to 50%/75% in 2014 (when Yahoo and Hotmail deployed STARTTLS), to 75%/80% in 2016 (when Gmail rolled out insecure indicators for non-STARTTLS mail)
    - Roughly 92-93% for both inbound/outbound in 2019
* Long tail of operators (2015):
    - 80% of Alexa Top 1M domains supported STARTTLS
    - 34% had certificates that matched mail server
    - Only 0.6% had certificates that matched domain (only way to authenticate recipient mail server)

### SMTP MTA Strict Transport Security (MTA-STS)

* Mail service providers can declare ability to receive SMTP over TLS; specify that messages must only be sent over TLS

### Authenticating email

* 81% of emails are protected by SPF and DKIM, but mostly by the biggest providers -- Google, Yahoo, Hotmail
* Again, big long tail; only 1% of 1M top domains support DMARC

#### Sender Policy Framework (SPF), 2006

* Sender publishes list of IPs authorized to send mail

#### DomainKeys Identified Mail (DKIM), 2011

* Sender signs messages with cryptographic key
* Receiver looks up key to validate message
* But: no way to convey expectation that message should have signature; can simply strip the signature in an attack

#### Domain Message Authentication, Reporting, and Conformance (DMARC), 2015

* Specifies policy for what a receiver should do if it encounters a message that fails SPF and DKIM validation

### PGP: Pretty Good Privacy

* Third-party toolkit for encrypting and signing emails; originally delivered in 1991
    - Essentially: encrypt email with recipient's public key, copy-paste resulting ASCII ciphertext and send it; recipient decrypts with their private key
* Usability and implementation challenges: many clients fail to validate signatures properly (2019)
* Signatures prevent deniability when data is later leaked

### Mail server vulnerabilities

* 2021 ProxyShell RCE attack on Microsoft Exchange server
    - Allowed attackers to dump mailbox content on Exchange mail servers
* In general, local/enterprise mail server ecosystem is super insecure - better to use cloud providers e.g. Google, Exchange Azure if needed

## Short Message Service (SMS)

* SMS allows sending 140-byte messages as part of non-data cellular protocols (e.g. GSM, CDMA, HSPA, 4G, 5G)
* Messages sent using same type of control messages used to coordinate with cell towers for service
* No end-to-end security protections; provider sees everything -- messages stored and forwarded by provider

### Phone number and SMS hijacking

* Recently: increase in social engineering attacks (e.g. convincing telco frontline employees) to hijack phone numbers
    - Used to get SMS 2FA tokens
* Until recently, a database called NetNumber allowed some companies to change routing of numbers (e.g., create exception for SMS messages coming from a landline to some third-party mailbox)
    - To request, just fax NetNumber with a (maybe fake) authorization letter from the telco saying the customer requested this
    - This allowed diversion of any cell phone's SMS messages to an arbitrary mailbox!
    - This still exists, but in 2021 an article about this was published, and now NetNumber only services landline numbers

## Off-the-Record messaging (OTR), 2004

* Alternative to PGP on top of instant messaging clients (e.g. Jabber)
    - PGP has no forward secrecy: if you get someone's RSA private key, that reveals all past and future messages
* Precursor to today's secure messaging protocols
* Beyond encryption and authentication, introduced new ideas to messaging security:
    - Forward secrecy: messages encrypted with temporary per-message AES keys negotiated using Diffie-Hellman key exchange protocol. Compromise of long-lived keys does not compromise any previous conversations
    - Deniability: messages do not have signatures; anyone is able to forge a message to appear to have come from one of the participants in the conversation

## Signal Protocol, 2013

* Protocol created by creators of Signal app; previously called Axolotl protocol
    - Built on good parts of OTR and Silent Circle Instant Messaging Protocol (SCIMP)
* Basis for Signal, WhatsApp, Google, E2E Encryption
* Based on notion of "double ratchet" between each message
* This works! Solved secure messaging for chats between two people

## Messaging Layer Security (MLS) protocol

* RFC in active development that sets out to create a protocol for asynchronous group keying with forward secrecy and post-compromise security
    - Much more complicated than Signal protocol
    - Difficult to deal with when someone leaves the party
* But: still unsolved