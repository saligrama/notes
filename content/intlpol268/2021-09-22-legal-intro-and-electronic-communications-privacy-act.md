# Legal intro

## Course assignments: Short responses
* Assigned Wednesday lecture; due 11:59pm following Tuesday
* Max 400 words
* Discussion with other students allowed but need to submit alone
* Grading: "needs improvement," "meets expectations," "exceeds expectation"

# Cyber issue forces at play
Pathetic Dot theory: Market, Law, Norms, Architecture

Industry standards, compliance bodies, self-regulatory schemes

* ISO27001 - infosec management standards; can become legal req by law or contract
* PCI DSS - anti CC fraud
* Digital Advertising Alliance - ad industry self-reg body

Tech companies enforcing standards + policing behavior

* Apple, google app stores - rules and review processes; keep out malware/spyware
* Cloudflare - stopped providing DDoS protection for hate speech

Insurance

* Cyber risk insurance - induce improvements in cybersec
* Forces are entwined with the law but not separate
    - Legal regulations for insurance, app store antitrust issues
    - Laws still necessary because self-regulation is lax

# Electronic Communications Privacy Act of 1986 (ECPA)

## 4th amendment
Government cannot engage in unreasonable search and seizures

* SCOTUS: 4A protects conversations from warrantless surveillance
    - In-person conversations: *Berger v. New York*, 388 U.S. 41 (1967)
    - Phone calls: *Katz v. United States*, 389 U.S. 347 (1967)
* 4A does not protect phone numbers held by third parties
    - *Smith v. Maryland*, 442 U.S. 735 (1979) - police can record outgoing numbers dialed from a phone
* 4A doesn't protect info disclosed to third parties (business records)
    - *United States v. Miller*, 425 U.S. 435 (1976) - seizing bank records with grand jury subpoenas is constitutional

## Congress's response
* Original federal Wiretap Act (1968) passed after *Berger* and *Katz*
* In 1986, Congress passed ECPA
    - Amended Wiretap Act to include transmission of electronic data via computers
    - Added Stored Communications Act
    - Added Pen Register Statute - applies to transactional info about wire, electronic communications

## ECPA definitions
* **Electronic communication**: email, text, non-voice internet transmissions, faxes, but not beepers, tracking devices, pr EFT info (electronic monetary transfer between financial accounts)
    - How does this apply to VoIP or Zoom calls? Unclear!
* **Contents**: "substance, purport, or meaning" of the communication
    - ex. subject line + body of an email (or contents of a snail-mail letter)
* **Non-content** information: information about the communication
    - Metadata
    - Email header, web request header, etc
* Note: in the context of the internet, this distinction is unclear

### ECPA and Wiretap Act 
* Prohibit interception of contents of wire/oral/electronic communications except as authorized
* However - Not unlawful if you're "a party to the communications" or "one party has given prior consent to interception"
* One-party vs all-party consent: Wiretap Act - one party; whereas California, Massachusetts, etc - all-party
    - If interception isn't a federal violation, it might still be a state violation
* Wiretap orders to law enforcement aka "super warrant" or "Title III orders"
* Limited suppression remedy if evidence is gathered in violation of the law
    - Evidence must be thrown out in court
    - Only applies to oral and wire communications but not electronic text communications

### Pen Register Statute
* Prohibits interception of non-content parts of electronic communications except as authorized
    - Govt's application must certify that info "likely to be obtained" is relevant to "ongoing criminal investigation being conducted by that agency"
    - Court must issue the order if it finds the gov't has certified this to the court
* Regulates real-time collection of communication's non-content info
    - Dialing, routing, addressing, and signaling (D/R/A/S) info
        - this gets a communication from point A to point B - incoming/outgoing phone numbers, email to/from addresses, IP addresses
        - URL can be DRAS or DRAS and content (i.e., search query in URL string)
    - Govt can only collect DRAS info and not contents
* Technical assistance provisions

### Stored communications act
* Regulates access to, and disclosure (both voluntary and compelled) of, stored communications, records, and subscriber info held by third-party service providers (ISPs, email, social media, cloud, etc)
* "Electronic communications service" (ECS) vs "remote computing service" (RCS) providers:
    - ECS: "any service which provides to users thereof the ability to send and receive wire or electronic communications"
    - RCS: "the provision to the public of computer storage or processing services by means of an electronic communications system"
* Different rules apply depending on whether ECS or RCS
* Written before modern internet; courts recognize that nowadays, most modern tech platforms act as both
    - *Theofel vs. Farey-Jones*, 359 F.3D 1066 (9th Cir, 2004) effectively collapsed ECS/RCS destinction
    - Courts still examine which feature of service is at issue (what "hat" is the provider wearing)