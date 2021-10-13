# Computer Fraud and Abuse Act (CFAA), continued

*hiQ v. LinkedIn* (oral arguments Oct. 18, 2021 - remanded to 9th circuit)
* hiQ argument:
    - Van Buren's gates up/down inquiry means scraping publicly avail data is OK (gate up) but scraping private data (like in Power Ventures) is not (gate down)
    - C&D letters and counter-bot measures don't count as "down gates" since info is public
* LinkedIn argues:
    - Public data counts as "authorization", but LinkedIn put the "gate down" with C&D letter, targeted IP blocks, so after that hiQ access was without aughorization
    - IP blocks are technological or code-based rstrictions on access, per Van Buren footnote 8

*Southwest Airlines v. Kiwi.com* (N.D. Tex. Sept. 30, 2021)
* Kiwi scraped flight data from Southwest Airlines website, resold flights + additional fees
* Southwest sent C&D letters to Kiwi and implemented tech. countermeasures
* Claims in lawsuit: CFAA, TX "mini-CFAA," breach of contract, TM infringement, etc
* Southwest gets injunction to make Kiwi stop scraping, based on breach of contract claim
    - Court rejects Kiwi's argument that it hadn't agreed to Southwest's terms
* Court says hiQ doesn't matter here, because that's a CFAA case (and hiQ even says there can be a breach of contract claim even if no CFAA claim) and it was vacated by SCOTUS post-*Van Buren*
* Kiwi will likely appeal to 5th Circuit

**Takeaway**: law of scraping is still murky, so there's still legal risk after *hiQ* and *Van Buren*

## What laws can be used other than CFAA?
Other laws have been invoked alongside CFAA in civil and criminal cases
* Federal law
    - Copyright infringement (*Cvent*, *AP v. Meltwater* (S.D. N.Y. 2013), *Compulife v. Newman* (11th Cir. 2020))
        - *Meltwater* and *Newman* are web-scraping suits but not filed under CFAA
    - Lanham Act (trademark infringement, unfair competition) (*Cvent*, *Kiwi*)
    - Trade secret misappropriation (*Nosal*)
    - ECPA (*Google Safari Cookie*)
    - Honest services fraud (*Van Buren*) (criminal only)
    - Wire fraud (*U.S. v. Nicolescu* (6th Cir. Oct. 5, 2021) (criminal only)
* State and common law
    - Mini-CFAA (*Cvent* (VA), *Power Ventures* (CA), *Newman* (FL), *WhatsApp v. NSO Group* (CA), *Kiwi* (TX))
    - Trade secret misappropriation (*Compulife*)
    - Unfair competition
    - Breach of contract (*Cvent*, *Kiwi*, *NSO*)
    - Fraudulent inducement
    - Tortuous interference with contract (where defendant causes *other* users to break TOS)
    - Breach of privacy (ACLU lawsuit against Clearview AI under IL Biometric Information Privacy Act)
    - Unjust enrichment (*Cvent*, *Kiwi*)
    - Trespass to chattels (*ebay v. Bidder's Edge* (N.D. Cal. 2000), *Intel v. Hamidi* (Cal. 2003))

Claims may not necessarily succeed, and may be widely considered wrongly decided if they do (e.g., *Newman*, *Bidder's Edge*), but being sued is still an issue!

## CFAA impact on security research
* *MBTA v. Anderson* (D. Mass. 2008): MIT students gagged from presenting research on vulnerabilities in transit card system until conference over
* *Sandvig* lawsuit: social sciences researchers sued DOJ under first amendment, claiming thei feared CFAA prosecution
    - Research was on algorithm-based discrimination wrt housing and jobs
    - TOS prohibitions: Bots, scraping, crawling, sock puppets
    - Court ruled in 2018 that 1030(a)(2)(C) doesn't covered these accesses, because "exceeds authorized access" is about access restrictions, not use restrictions
    - Ruled in 2020 that TOS violation is not a CFAA violation, and that creating accounts using false info isn't "bypassing an authentication gate" because the credentials are authentic and non-stolen
    - Case dismissed as moot because proposed research didn't violate CFAA; researchers appealed, but dropped appeal after *Van Buren*

### How will *Van Buren* affect security researchers?
Commentators are mostly optimisitic
* Tim Edgar (Brown University): *Van Buren* is good for two reasons
    - Narrows scope of CFAA; will promote companies working with rather than against security researchers
    - Companies will be forced to step their game up wrt security
* EFF: *Van Buren* means it's not a crime to violate TOS limiting purposes for accessing info or restricting later use of data
    - But notes that FN8 left unresolved whether "gates down" means only technological limits or also contractual/policy limits
* Riana Pfefferkorn: Congress should reform CFAA
    - Voatz, hacked mobile voting company, reacted to *Van Buren* by downplaying its importance and signaling a continued willingness to go after researchers
    - Unclear that CFAA actually deters "black hats" and has a chilling effect on "white hats"
    - Instead of litigation to resolve questions that SCOTUS left open, Congress should act to narrow the statute and protect good-faith research

# Digital Millenium Copyright Act (DMCA)

## DMCA sections
* 1201: Anti-circumvention provisions
    - Acts of circumvention (a)(1)
        - `No person shall circumvent a technological measure that effectively controls access to a work protected [by federal copyright law]`
    - Trafficking in circumvention tools (a)(2), (b)(1)
        - `No person shall manufacture, import, offer to the public, provide, or otherwise traffic in any technology, product, service, device, component, or part thereof` for circumvention of technological measure
    - What are technological protection measures?
        - e.g. DRM: region codes for DVDs, encryption for HD DVD and Blu-Ray, copy-protection on movies and games
        - Chip/software authentication handshakes
        - Secret handshake authentication sequence between game software and the gaming server (e.g. WoW game + Blizzard servers)
    - What are circumvention tools?
        - Technology/product/service/etc that
            1. is primarly designed or produced for the purpose of circumventing [an access-control or copy-control TPM];
            2. has only limited commercially significant purpose or use other than to circumvent; or
            3. is marketed by that person or another person acting in concert with that person with that person's knowledge for use in circumventing protection.

            **Example**: DeCSS
    - This section is less enforced today:
        - Exemptions may be effective
        - Market changes since early 2000s, DRM largely ineffective and shifts away from CDs/DVDs to streaming
        - Chilling effects = less research activity
        - Corporate pushback against the chill - Microsoft/Github
    - Exemptions:
        - 1201(f) Reverse engineering for interoperability
            - Circumvention of TPM is OK for making programs interoperable with each other, but need to jump through a *lot* of hoops
        - 1201(j)(1) Security testing exception
            - Need authorization from computer system/network owner to do good faith testing/investigation/correction of security flaws or vulnerability - otherwise subject to DMCA liability
                - Still cannot violate copyright, CFAA, other laws
        - 1201(a)(1)(C-D) DMCA Triennial Rulemaking
            - Librarian of Congress has power, upon recommendation of the Register of Copyrights, to grant 3-year exemptions
                - Lots of hoops to jump through, and only limited immunity
                - Exemptions expire after 3 years
            - Security research exemption was significantly expanded in 2015, 2018, and 2021
        - Others for libraries, law enforcement, etc.
* 1203: Civil remedies
    - Injunctions
    - Impounding
    - Damages:
        - Actual damages + profits, or
        - Statutory damages:
            - 1201: $200-$2,500 per violation
            - 1202: $2,500-$25,000 per violations
        - Damages go up for repeated violations or down for innocent violation
    - Costs and attorney fees
* 1204 - Criminal penalties
    - If willful, for purpose of commercial advantage/private financial gain, then up to $500,000 fine and/or 5 years in prison

## CFAA uses
* Secure Digital Music Initiative threat against researchers (2000)
    - Princeton prof. Ed Felten et al. defeated watermarking technologies to protect digital music files
    - SDMI threatened them with DMCA liability
    - Researchers withdrew findings from academic conference
    - Ultimately presented at other conference
* *U.S. v. Skylarov/Elcomsoft* (N.D. Cal. 2001)
    - Dimitry Skylarov, Russian citizen, created Software for Adobe e-book owners to convert from e-book format to PDF for his employer Elcomsoft
    - Presentation at DEFCON 2001 in Las Vegas
        - This fell afoul of DMCA marketing provision, even though the actions were legal in Russia
    - Prosecuted in Northern California since Adobe HQ is in San Jose
        - US went after Elcomsoft and dropped charges against Skylarov in exchange for his testimony against his employer
* *Green et al. v. U.S. Department of Justice* (D.D.C 2016)
    - Intel threatened to sue Andrew Huang, computer scientist/inventor, over a digital video device he wanted to make
    - Matthew Green, computer science professor at Johns Hopkins University, wanted to publish security book
    - Green: 2015 security research exemption did not cover desired research and books
    - June 2019 court decision: partially denied govt's motion to dismiss the case
        - Dismisses some of plaintiffs' claims, but rules plaintiffs adequately alleged that 1201 is unconstitutional as applied to desired contucts
        - "Code is speech": DMCA and triennial rulemaking process burden use and dissemination of computer code, thereby implicating First Amendment
        - DMCA prohibition on circumvention and trafficking burden 1st Am-protected agtivities
        - Govt doesn't show that 1201's anti-circumvention and anti-trafficking provisions don't burden more speech than necessary to further govt's interest in prohibiting piracy
    - July 2021 decision: denied plaintiffs' request for injunction against prosecuting them
        - Green's book describing how to circumvent TPMs likely would not trafficking in a TPM circumvention tool, so no crime, no injunction needed
        - Huang denied an injunction because unlikely to succeed on merits of 1st Am claim, govt interest in protection copyrighted works from privacy outweighs speech interest in trafficking device that would facilitate privacy of streaming video, games, DVD/Blu-Ray, etc
    - Plaintiffs filed an appeal to the D.C. Circuit in September 2021
* *Apple v. Corellium* (S.D. Fla. 2019)
    - DMCA means that one can violate copyright laws without copyright infringement
    - DMCA 1203 allows civil lawsuits
    - Corellium makes an iOS virtualization tool used for security testing research
    - Alleged anti-trafficking violations under 1201(a)(2) and 1201(b) for "trafficking in products that are used to modify iOS and circumvent technological controls that protect copyrighted works"
    - In 2020, courts dismissed Apple's claims for copyright infringement on the basis of fair use (but let DMCA claims move forward) - Apple now appealing that ruling to 11th Cir.
    - In August 2021, parties settled DMCA suit, Corellium can keep selling its product