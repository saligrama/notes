# Data Security and Data Breach Notification Laws

Nothing comprehensive at the federal level; states have all the power here.

## State Data Breach Notification Laws
* Each state, plus DC/PR/VI/GU have one
* Laws share many commonalities, but plenty of variation
* Discovering/being notified of a "breach of the security of the system" triggers duty to notify
* Any state resident whose "personal information" (PI) was, or is reasonably believed to have been, acquired by an unauthorized person
* Definitions comonly used by many states:
    - Breach of security of system: unlawful, unauthorized acquisition of PI
    - PI: first name/initial + last name + 1 more of
        - SSN, DL/ID card number, account/credit/debit number + pin/code/password
    - Laws generally apply to computerized data that includes PI
    - Excludes publicly available information lawfully made available to the public by government or media
    - Many states add more types of info to definitions of PI

Ex: in California
* Need to notify attorney general (AG)'s office if organization has data breach
* AG's office curates list of data breaches on website; searchable by name and date; includes both private entities and CA public entities

Some states require notification ASAP
* Criticism is that this forces notification before it is clear what exactly happened; vague initial notices can sow fear/uncertainty/doubt among public

## State Data Security Laws

* About half of states have these; some apply to business only, government only, or both
* Number of states with these has doubled since 2016
* Some commonalities with a lot of state-by-state variance
* In general, businesses that own, license, or maintain "personal information" about a state resident must
    - Implement and maintain "reasonable security procedures and practices" appropriate to the nature of the information
    - Protect the personal information from unauthorized access, destruction, use, modification, or disclosure
* Some states have "sectoral laws"; e.g. payment card info, health data, etc
* Some states impose specific security requirements (e.g. CO, MA, NY)
    - MA sued Equifax over breach since Equifax violated many provisions of its data security law; recently settled for millions of dollars

### The evolution of California data security laws
* 2016: CA data security law: reasonable security procedures if maintaining personal data
* 2019: Equifax addition: consumer credit reporting ages need to do software updates ASAP
* 2020: California Consumer Privacy Act (CCPA) (see: ECPA lecture): allows individuals to sue companies for punitive ($100-$750) or actual damages if "nonencrypted and nonredacted" PI is exfiltrated
* 2023: California Privacy Rights Act (CPRA): expands definition of personal information

### NY Department of Financial Services regulations
* 2017: NYDFS enacted Cybersecurity Regulation; specifies minimum cybersecurity requirements for all covered financial institutions
    - Periodic risk assessments
    - Audit trail for cybersecurity events
    - Data retention limits and access controls for PII
    - Incident response plan
    - Encryption of sensitive data
    - Deploy multifactor authentication
    - Annual compliance certification
* Criticism: rules may be rigid and unworkable
* First enfocement action: July 2020, against First American Title Insurance Co.
    - Vuln dating back to May 2014 detected in December 2018, and company waited 6mo+ to notify customers
    - Anyone with a web browser could access millions of customers' PI dating back 16 years
* First penalty: March 2021, $1.5 million, against Residential Mortgage Services Inc.
    - Routine compliance exam revealed RMS hadn't disclosed 2019 data breach or done required risk assessments
* Now, FTC wants to be more like NYDFS
    - Gramm-Leach-Bliley Act "Safeguards Rule": FTC is one of several agencies that GLBA gives regulatory and efnorcement authority re: how financial institutions protect consumer info
    - FTC wants to amend Safeguards Rule to imitate NYDFS regs

## Federal Agency Enforcement

### SEC Breach Notification Requirements (and Data Security)
* Public companies must report "material" events to shareholders
    - "Material": substantial likelihood that info might be important to make an investment decision
* 2018 guidance on cybersecurity disclosures:
    - SEC requires companies to disclose factors that make investments in company's securities speculative or risky
    - This includes cybersecurity risks or incidents
    - Disclosure of risk factors should be tailored, not generic
    - Disclosures must be "timely"; ongoing investigation may affect scope of disclosure but does not alone justify nondisclosure
* March 2021 report by IT security firms: public companies aren't following on SEC guidance
    - Too much vague legalese boilerplate, leaving investors in dark re: actual risks and actual attacks
    - Recommends private companies should provide more detail and candor to SEC
* SEC's current SolarWinds probe: document requests to hundreds of companies
    - Companies affected by SolarWinds Russian hacking operation being asked for records re: "any other" data breach or ransomware attacks since October 2019
    - Fear of liability if probe reveals breaches that should have been disclosed, or poor internal security controls
        - Data security: 3 recent enforcement actions under Reg S-P "Safeguards Rule"

#### SEC Yahoo Settlement (April 2018)
* Yahoo misled investors by failing to disclose one of the largest data breaches ever
* Dec. 2014: Information security team learned that Russian hackers had stolen PII for hundreds of millions of user accounts
* SEC alleged Yahoo failed to properly investigate breach circumstances and didn't adequately consider whether it needed to disclose to investors
* Over 2 years before breach was publicly disclosed - in 2016, when Yahoo was being acquired by Verizon
    - Note: MA AG sued Equifax for taking 40 days to disclose!
* SEC also alleged that during that 2-year period, quarterly and anual SEC reports didn't disclose breach; only said Yahoo faced the *risk* of breaches
* SEC and Yahoo settled for $35 million penalty

### FTC: Fair Information Practice Principles (1998)
* Notice/Awareness
* Choice/Consent
* Acess/Pariticipation
* Integrity/Security: requires organizations to protect the quality and integrity of PI
* Enforcement/Redress

FTC section 5 authority: "Unfair or deceptive acts or practices in or affecting commerce...are...declared unlawful"
* Ability to levy fines
* Ability to define adequate practices

FTC v. Wyndham Hotels (3rd Cir. 2015)
* FTC has Section 5 authority to regulate data security practices
* Wyndham had fair notice of potential "unfair" prong liability under FTC Act, due to FTC's previous adjudication and interpretive guidance
* Settled: 20-year consent decree, security focused practices, audits

FTC v. LabMD (11th Cir. 2018)
* LabMD argued that without formal agency rulemaking, LabMD was not on notice of what's unreasonable
* Assumed without deciding that poor data security is an unfair practice
* Court sided with LabMD
* Pos LabMD, consent orders won't be as useful to police future bad behavior; now FTC data security orders have gotten more specific

FTC sues everyone!
* Dozens of 5 enforcement actions re: inadequate consumer PI protection since 2002
* FTC consent orders require creation of comprehensive info security programs -> FTC developed a set of de facto security standards and practices
* Failure to comply = fines for violated the order
* Requirements for act or practice to be unfair:
    - "causes or is likely to cause substantial injury to consumers"
    - "which is not reasonably avoidable by consumers themselves"
    - "and is not outweighted by countervailing benefits to consumers or to competition"

Examples:
* CVS Caremark (2009)
* Facebook (2011)
    - Facebook (2019) Cambridge Analytica - violation of 2011 decree - $5 billion fine
* Google (2011)
* Twitter (2011)
* Uber (2017)
* Lenovo (2017)
* PayPal/Venmo (2018)
* D-Link (2019)
* Equifax (2019)

# Class Action Litigation
Lawsuits by people whose data was breached
* Example: Yahoo
    - Class action lawsuit over 3 separate breaches affecting a total of 3 billion accounts
    - Settlement of $117.5 million in April 2019
    - Around 194 million people in the US and Israel may be eligible to make claims

Securities class action (by shareholders): still very effective
* Yahoo: $80m, another $29m settlement
* Equifax: $149 million