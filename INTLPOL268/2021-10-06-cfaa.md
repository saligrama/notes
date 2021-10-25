# Computer Fraud and Abuse Act (CFAA)

***TW: Suicide***

## Key concepts

* "Access without authorization"
    - Not defined in the statute
* "Exceeding authorized access"
    - Defined in the statute yet courts still are inconsistent about interpretting it
    - Recently interpreted by the Supreme Court in *Van Buren v. United States*

## Subsections:
1. a - Seven or more prohibitions
    * (a)(1) Espionage prohibitions
    * (a)(2) Obtaining information from 3 sources: financial/banking/credit institutions, federal govt, or "any protected computer"
    * (a)(3) Trespass on federal government system
    * (a)(4) Intent to defraud
    * (a)(5) Causes damage
        - (A) knowingly causes transmission of program that intentionally causes damage without authorization to a protected computer
        - (B) intentionally accesses a protected compute rwithout authorization, and as a result of such conduct, recklessly causes damage
        - (C) intentionally accesses a protected computer without authorization, and as a result of such conduct, causes damage and loss
    * (a)(6) Password trafficking
    * (a)(7) Extortion threats to a computer/data
        - Renewed relevance in the age of ransomware
2. b - Attempt and conspiracy
3. c - Sentences for criminal violations
4. d - Secret service power to investigate
5. e - Definitions
    * "damage": any impairment to the integrity or availability of a data, a program, a system, or information
    * "loss": any reasonable cost to any victim: includes cost of incident response, damage assessment, data/program/system/information restoration, revenue lost, cost incurred, other consequential damages incurred because of interruption of service
    * "protected computer*:
        - (e)(2)(A) Financial or federal government computer
        - (e)(2)(B) "used in or affecting interstate or foreign commerce or communication" -> any internet-connected computer
        - (e)(2)(C), added 2020: "is part of a voting system", either is used for federal election management/support/administration or "has moved in or otherwise affects interstate or foreign commerce"
6. f - Law enforcementand intelligence agencies exception
7. g - Civil cause of action, negligence exemption
8. h - Reporting to Congress (now expired)
9. i - Forfeiture provisions
10. j - Forfeiture provisions

## Criminal cases under CFAA

### Morris Worm (1988)
* One of the first computer worms spread via Internet
* At least $100,000 in damage ($225,000 in 2021 dollars)
* Infected ~3-10% of all Internet-connect computers at the time
* First felony conviction under CFAA (upheld by Second Circuit, 1991)
* Convicted under then-current version of 1030(a)(5) which was subsequently amended in 1996

### Indictments of state-sponsored hackers and look-alikes
* China: Dec. 2018 indictment of members of APT-10 for conspiracy to violate 7 sections/subsections of CFAA
* Iran: Mar. 2018 indictment of Iranian "Hackers for hire" who hacked 144 U.S. universities at behest of Iranian govt, conspiracy to violate 5 sections/subsections of CFAA
* Andrei Tyruin: Russian who hacked banks and financial institutions such as JPMorgan (original CFAA offense)
    * Pleaded guilty in fall of 2019, sentenced to 12 years in prison in 2021
    * Turned out to be plain old organized crime, but hacks' scope and sophistication = originally suspected as state-sponsored attack

### United States v. Lori Drew (C.D. Cal. 2009)
* Mother created a fake myspace account to cyberbully a girl she thought was spreading gossip about her daughter; daughter took her own life
* This constituted terms of service violation of MySpace
* Lower court held that terms of service violation is "unauthorized access"
* Judge overturned conviction

### United States v. Nosal I (9th Cir. 2012)
* Group of employees log into company computer and take info from confidential database for use by a competing business
* 9th Cir. ruled that this did not exceed authorized access
* Violating a website's TOS can't be the basis for criminal liability under the CFAA; otherwise, could criminalize many daily activities

### United States v. Nosal II (9th Cir. 2016)
* This time, govt tried to prosecute on access "without authorization"
* Former employee's login gets revoked; other emloyees who left logged in using credentials of current employee
* 9th Cir. ruled that access was "without authorization"
* After affirmative revocation of authorization; no "going through the back door and accessing the computer through 3rd party"
* Note: prosecutors stacked other (non-CFAA) charges and jury convicted on all counts
    - Companies really like punishing departing employees that try to compete
        - Can sue departing employee civilly, even if prosecutors refuse to charge
        - TOS, employment agreements, etc exist to protect provider or employer, not user or employee

## Civil cases under the CFAA
* Web scraping
    * Cvent v. **EventBrite** (EDVA 2010)
    * **hiQ** v. LinkedIn (9th cir. 2019) (vacated)
* ToS violations
    * Cvent v. **EventBrite** (EDVA 2010)
    * **hiQ** v. LinkedIn (9th cir. 2019) (vacated)
* Changing/masking IP address
    * **Craigslist** v. 3Taps (NDCA 2013)
    * **Facebook** v. Power Ventures (9th Cir. 2016)
* Ignoring cease and desist letter
    * **Craigslist** v. 3Taps (NDCA 2013)
    * **Facebook** v. Power Ventures (9th Cir. 2016)
    * **hiQ** v. LinkedIn (9th cir. 2019) (vacated)

### Facebook v. Power Ventures (9th Cir. 2016)
* Issued one week after *Nosal II*: Orin Kerr called it an "outlier"
* What does "without authorization" mean?
* FB users' consent to give Power Ventures access to their info on FB doesn't make it OK
* Per *Nosal I*, violating TOS is *not* violating CFAA
* Merely bypassing IP block is *not* unauthorized use
    - May not know authorization was revoked
* *Does* violate CFAA to keep accessing servers without permission after getting a C&D letter

### hiQ Labs v. LinkedIn (9th Cir. 2019) (vacated and remanded by SCOTUS in 2021)
* Scraping publicly-available data isn't a CFAA violation
    - Even if system owner sends a C&D letter!
* What's "without authorization"?
    - *Power Ventures*: sending a C&D letter revokes authorization
    - LinkedIn sent hiQ a C&D; yet court says no CFAA violation
* Limiting *Power Ventures*: Data viewable only when logged-in (CFAA violation) vs. data publicly displayed to everyone (not a CFAA violation)
    - CFAA violation would

## Van Buren v. United States (2021)
* Police officer accessed database for private purposes; state prosecuted
* Case interprets "exceeds authorization" clause
* Justice Barrett's majority opinion agrees that "without authorization":outside hackers; "exceeds authorized access":"inside hackers"
* "Exceeds authorized access" means "obtain[ing] information from particular areas in the computer -- such as files, folders, or databases -- to which [your] computer access does not extend."
* Agrees with *Nosal I*'s access vs. use distinction
* Holds that TOS violations do not violate the CFAA
    - Court is wary of "the drafting practices of private parties," who write computer use policies and TOS, control what access is a federal crime

### "Gates-up-or-down question"
* Majority: Liability "stems from a gates-up-or-down inquiry - one either can or cannot a computer system, and one either can or cannot access certain areas within the system [8]."
    - Footnote [8]: "For present purposes, we need not address whether this inquiry turns only on technological (or 'code-based') limiations or access, or instead also looks to limits contained in contracts or policies"
* Leads to open questions:
    - How will courts apply this to "unauthorized access" cases?
    - What counts as a "gate" and what counts as "up or down"?
    - Will you necessarily know when a gate is there and you're bypassing it? (see 9th. Cir. re: IP blocks)
    - What will courts do with Footnote 8?
    - What does this decision mean for bug bounty programs?
        - What if bug bounty terms say "you may test database X but not database Y?"
    - What does this decision mean for web scraping?
        - We may soon find out, since 9th Cir. needs to reevaluate *hiQ v. LinkedIn*
            - Oral arguments Oct. 18, 2021 - but hiQ labs went out of business in 2018