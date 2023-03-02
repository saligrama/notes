# TLS and Modern Cryptography

## Some history: 2014 state of encryption

* 14% of Alexa Top Million sites supported HTTPS
    - Many: support HTTPS only to redirect you back to HTTP
    - Higher adoption than average sites
* Most sites used known-weak versions of TLS
    - Only 1 of 4 popular sites supported latest TLS 1.2
* 4% of sites supported perfect forward secrecy (PFS)
* Only 1 out of 3 emails encrypted when sent across the internet

## More history: US export-grade cryptography

* Until 1992: US considered cryptography a **munition**
    - Severely restricted exports of cryptography outside country due to cold-war concerns
    - NSA influence: US exports broken crypto, since NSA no jurisdiction in US
        - "people in a random coffee shop shouldn't be able to break foreign crypto, but NSA should"
    - Most browsers just used broken crypto everywhere
* Key length restrictions:
    - Public-key crypto: max 512-bit public keys
    - Symmetric crypto: max 40-bit keys
    - Signatures and MACs unregulated
* Early 1990s: two versions of Netscape browser:
    - US version w/ full-strength crypto e.g. 1024-bit RSA, 128-bit RC4
    - Export version: 40-bit RC2, 512-bit RSA
* 1996: Bernstein v. United States: Daniel Bernstein sued US government, saying that right to publish software w/ full crypto was 1st amendment right
    - 9th circuit 3-judge panel ruled in his favor, so crypto export regulations were ruled unconstitutional
    - US gov appeals, full 9th circuit overturns -- no precedent set
    - Bernstein sued again, but Clinton admin released executive order delegating export regulation authority to Commerce Department (rather than State)
        - Now: crypto allowed to be exported (and published open-source), as long as Commerce Department is informed
        - In practice: regulation is ignored; cryptography effectively unregulated

## SSLv2 (broken)

### Process

1. Client hello: random, client supported ciphers provided
2. Server hello: random, server supported ciphers provided
3. Client master key: cipher, `mk_clear`, `Enc_pk(mk_secret)` (client selects cipher)
4. Agree on: `write_key, read_key = KDF(cipher, mk_clear concat mk_secret)`
5. Server verify: `Enc_swk(random_client)`
6. Client finished: `Enc_cwk(random_server)`
7. Server finished: `Enc_swk(session_id)`

### Problems

* No commitment to handshake messages
    - MITM can force downgrade without knowing keys, incl. to bad ciphers
    - Fixed to non-HMAC MD5 hash function
        - No collision resistance, does have preimage and second-preimage resistance
        - MAC is not an HMAC, just a keyed hash -- vulnerable to length extensions
    - No concept of cert chains, just leaf certs
    - Only stream cipher is RC4 (broken)
    - Block ciphers all used in CBC mode: padding oracle issues
* 2016 Drown attack: sites used same pk/sk with both SSLv2 and TLS, breaking TLS security
* However: general principles provided base for TLS

## TLS

### Changes from SSLv2

* Client hello: ciphers define more hash types
* Server now chooses the cipher from the client hello
* Certificate process now supports cert chains (intermediate certs)
* Client key exchange based on premaster secret
* Session finished fixes cipher selection MITM

### Cipher suites

Defines key exchange, signature and hash if needed, and symmetric encryption used for a connection, e.g.

* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_EXPORT1024_WITH_RC4_56_MD5`
* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`

### TLS 1.1 (2006)

* Implicit initialization vector (IV) replaced with explicit IV to replace against CBC attacks
* Handling of padded errors to use `bad_record_mac` alert rather than `decryption_failed` alert

### TLS 1.2 (2008)

* MD5/SHA1 combination in PRF replaced with cipher-suite-specified PRF
* MD5/SHA1 combination in digitally signed element replaced with single hash
* Addition of support for authenticated encryption with additional data modes
* Extensions

#### Extension: TLS SNI (multiplexing on names)

* If more than one service per host, need to multiplex by some identifier (usually name)
* HTTP virtual hosting powered by the `Host` header; TLS exposes this via Server Name Indication (SNI) extension

### Attacks, 2012-2017

* BEAST (2012), Lucky 13 (2013) - attacks against CBC ciphers
    - Get rid of CBC!
* POODLE (2014), Sweet32 (2016), SHA-1 collision (2017)
    - Get rid of SSLv3, 3DES, SHA-1
* FREAK (2015), Logjam (2015), DROWN (2015) - attacks against export-grade ciphers
* Diffie-Hellman Key Exchange (see CS255 notes), finite-field version broken in practice (Adrian et al. 2015)
    - Why the attack works:
        - In practice, DHE uses standardized `p` (field being used), but that the only thing that precomputation of number field sieve (discrete log breaker) 
        - Precomputation is the only hard part of number field sieve! So in practice, easy (10 core min) to run the descent stage to decrypt practice
    - Why? Result of a miscommunication between cryptographic and systems community, so these primes are in an RFC
    - Logjam attack: MITM substitutes client `DHE` to `EXPORT_DHE`, server `EXPORT_DHE` to `DHE`, easy to **passively decrypt** the export-grade DHE at 512 bits
    - Mitigations
        - Browsers raised minimum prime size to 768 bits, with plans to drop all support for DHE
            - Asymptotically, breaking 768 or 1024 bit keys is hard
            - But: very parallelizable: building ASIC for <$100M (small fraction of NSA budget) can precompute for 1024-bit prime in a year
        - Servers disabled export ciphers, plans to move to ECDHE instead
        - In 2015: 82% of Apache vulnerable; in a year this went down to sub-10%, and now FFDHE has become completely deprecated

### TLS 1.3 (2018)

* Removed:
    - Problematic features from the past e.g. compression, renegotiation
    - Known broken ciphers like MD5, SHA1, RC4, 3DES, CBC mode, FFDHE, export ciphers, user-defined groups
    - Non-perfect-forward-secret handshakes, non-AEAD ciphers
* Added:
    - Simplified handshake with one fewer round-trip
    - Protection against downgrade attacks (e.g. signature over entire exchange)
    - Support for newer elliptic curves (e.g. x25519 and 448)
    - Zero-round-trip session resumption
        - Messages are replayable (no noce), leading to security flaws -- spec says not to handle requests that modify data until after the replay window is up (after server finishes handshake)
        - Primarily used by big tech
        - "Sketchiest part of TLS 1.3"
* Finalized in 2018 after ~5 years; involved academic community during design
    - Uncovered multiple attacks which were fixed in final version
    - Fixed issues with broken middleboxes: TLS 1.3 identifies itself as TLS 1.2 with an extension saying that the actual version is 1.3
* As of 2021: traffic into Cloudflare is about 70% TLS 1.3, 17% QUIC, 13% TLS 1.2
    - Web ecosystem looks good because browsers know how to update themselves well, everything else *sucks* (e.g. IoT embedded stacks, LDAP over TLS, SMTP [no cert validation!], etc.)
* Now: TLS 1.3 is pretty good! Generic communication stack should be gRPC over TLS 1.3, unless there's a good reason for otherwise (e.g. resource constraints -- see Noise)

#### More on AEAD (authenticated encryption w/ associated data)

* Why AEADs are good:
    - Use plaintext packet header as associated data (which can contain length, message type, etc)
    - AEAD functions like a stream cipher, with no padding needed -- output length matches input length + tag (MAC) length
    - Nonces can be sent in plaintext; randomly generated without reuse
* Issue: nonce management dependent on specific AEAD, not all need them. If nonce reused, catastrophic issues

## Noise protocol framework

* Noise: set of guidelines for authetnicated secure channels using Diffie-Hellman as only asymmetric primitive combined with an AEAD
    - No signatures
    - Parties: initiators, responders