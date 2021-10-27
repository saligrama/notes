# Ransomware and Foreign Hackers

***TW: discussion of death of a baby***

## The recent ransomware boom

Why is ransomware so big now? Cryptocurrency.

* Ransoms have been around for a long time, so has malware
* Historically, ransomware was used to hit small businesses and charge hundreds of dollars to recover data
* However, hackers now routinely extort critical infrastructure providers (gas pipelines, transportation systems, etc) for hundreds of *millions* of dollars
* Cryptocurrency is easier to obtain, transact, convert to real currency
* Exchanges are less uptight than backs about KYC (Know Your Customer)/AML (Anti Money Laundering) laws
* People generally think cryptocurrency is untraceable
    - However: this myth was busted in June 2021 after FBI recovered 63.7 of 75 bitcoins paid to DarkSide after Colonial Pipeline hack
        - DarkSide: "ransomware as a service" provider that sell ransomware payloads to other criminal/hacking groups
    - Ironically, crypto can be traced by FBI *faster* than bank transactions

## Criminal prosecutions of ransomware attackers

### Prosecution using CFAA
* CFAA has been repeatedly used by DOJ in ransomware-related incidents
* SamSam ransomware developers (both Iranian): indicted Nov. 2018
    - Victims: hospitals, municipalities, public institutions
    - 6 counts, including 1030(a)(5)(A), 1030(a)(7)(C)
    - Status: nothing has happened since Nov. 2018, presumably still in Iran
        - CFAA has little teeth against hackers in non-extradition nations
* Kelihos botnet operator (Russian): indicted 2017, extradited from Spain in early 2018
    - Allegedly used botnet to distribute JakeFromMars ransomware
    - 8 counts, including 1030(a)(4), 1030(a)(5)(A), 1030(a)(7)(C)
    - Status: sentenced August 2021 to time served + 3 years supervised release
        - Plead guilty to 4 counts: 1030(a)(5)(A), conspiracy, wire fraud, aggravated ID theft
    - Two co-conspirators were convicted and plead guilty to CFAA offenses in summer 2021
* Trickbot ransomware developer (Latvian): indicted 2020, arrested in Miami February 2021, arraigned in Ohio June 2021
    - Ransomware-as-a-service provider
    - Lived in Suriname, arrested when flying through MIA airport from Suriname
    - 47 counts, including conspiracy to violate (among others) 1030(a)(2)(C), 1030(a)(4), 1030(a)(5)(A), 1030(a)(7)(C)
    - Status: unknown, because case is under seal
    - Other Trickbot gang members still at large in Russia, Belarus, Ukraine, Suriname

### Other laws used to prosecute
* Wire fraud, 18 U.S.C. section 1343
    - Very common in hacking cases, along with bank fraud where relevant (Trickbot, APT 38)
    - Having a scheme to defraud someone or obtain money by false pretenses + sending a communication via the Internet for purpose of carrying out the scheme
        - Communication can be to an intended victim of the fraud scheme
            - Kelihos defendant used botnet to send "pump and dump" spam
        - Or: 
            - Doing online research to find potential victims (SamSam, APT 38)
            - Using victims' stolen bank logins to authorize money transfers (Trickbot)
            - Online communications among co-conspirators (APT 38) or with customers (Kelihos)
* Aiding and abetting, 18 U.S.C. section 2
* Conspiracy and attempt
    - Conspiracy to commit some other federal offense, 18 U.S.C. section 371
    - Conspiracy or attempt to violate the CFAA, 18 U.S.C. section 1030(b)
    - Conspiracy or attempt to commit wire fraud, 18 U.S.C. section 1349

## Civil litigation against victims of ransomware attacks

### Kidd v. Springhill Hospitals (filed June 2020)
* Alabama wrongful-death lawsuit against hospital (plus doctors, care team, etc.) by mother of infant who was delivered in distress during a ransomware attack and later died
* Distinct from data security and data breach notification laws
    - Not about PII being poorly secured, breached, or hacked
    - Allegedly improper care resulting in death because cyberattack took down crucial equipment
* Also distinct from CFAA because CFAA penalizes the hacker, not the hacked
* What causes of action are asserted?
    - Fraudulent non-disclosure - for not telling mother that ransomware had taken down hospital systems and placed patient care and saftety at risk, or sending her somewhere else to deliver her daughter
    - "Wantonness" - hospital had duty to provide appropriate medical care and wantonly failed to do so, in part because cyberattack had forced them to use outdated paper charts etc
    - Wrongful death - resulting from wanton failure to provide appropriate medical care
    - Negligence - negligently departed from the accepted standard of care, in part because electronic medical equipment and record-keeping systems unavailable due to cyberattack
    - Breach of implied contract - to safely provide medical care and nursing
* If ransomware attacker is ever caught, potential CFAA case?
    - Offense: 1030(a)(5)(A), 1030(a)(7)(C) - as seen in SamSam indictment involving several hospitals
    - Punishment:
        - 1030(c)(4)(F): "if the offender...recklessly causes death from conduct in violation of subsection (a)(5)(A)" - fine + up to life in prison
        - 1030(c)(3): for (a)(7)(c) - fine/prison (5 years first offense, 10 years otherwise)
* Could Kidd, the mother, assert a civil CFAA claim against the attacker?
    - Probably yes, but no court cases say so explicitly
        - *Cf. Wofse v. Horn*, No. 19-cv-12396 (D. Mass. March 2, 2021) - plaintiff alleged that defendants' cyberattacks adversely affected his health, court allowed civil case, but different CFAA subsection
* Expect more cases as more ransomware on hospitals cause more harm and deaths
    - Hospitals are not always treated as off-limits, some attackers bet hospitals have incentive to pay up
    - September 2021 CISA report says ransomware attacks on hospitals, coupled with COVID-19 caseloads, have pushed National Critical Function of providing medical care almost to the breaking point

## Potential liability of ransomware victims that pay ransom

### Ransomware: to pay or not to pay?
* Supply and demand: if crime doesn't pay (supply): maybe less crime (demand), incentive to punish ransomware payors
* Countervailing concern: if paying is punished, companies would still pay but not tell govt about the hack
    - Peters/Portman bill would provide money for recovery costs (ransomware remediation cost ~= $2M)
* Material support statutes: prohibit knowingly provide money to a Foreign Terrorist Organization
* Treasury Department's Office of Foreign Assets Control (OFAC)
    - OFAC sanctions list: individuals and companies owned, controlled by, or acting for/on behalf of, targeted foreign countries and regimes (Cuba, North Korea's Lazarus Group aka APT 38, terrorist orgs like Al Qaeda, drug traffickers like El Chapo)
    - Paying ransom to unknown hackers that end up on the list: OFAC will go after payors
    - Per Oct. 2020 advisory, OFAC will also go after those who facilitate payments to hackers on the sanctions list: i.e., cyber insurers, financial institutions, digital forensics and incident recovery firms, payment processors, consultants who help victims broker a deal with ransomware gang, etc
        - Nov. 2018: blacklisted two Iranians for exchanging SamSam ransoms from BTC to rial, depositing in Iran banks
        - Just blacklisted cryptocurrency exchange Suex for facilitating BTC transactions for ransomware actors
    - It makes no difference if facilitators/victims don't know the anonymous hacker is on the sanctions list
        - Hacker's IP address may be spoofed; also, China lets NK state-affiliated hackers work from China
    - In Sept. 2021, OFAC re-emphasized that paying ransoms threatens national security, may violate OFAC regulations
* SolarWinds: attributed to Russian SVR, aka APT 29, aka Nobelium
    - Motive: affected Treasury, State, DOJ, other federal agencies
    - Microsoft says goal was to obtain information about sanctions and US policy on Russia, plus counter-intelligence matters, methods for catching Russian hackers

### Alternative to payment: find a way to unlock the data yourself
* FBI discourages against paying ransoms but also doesn't support a ban on paying
* What if you already paid? FBI can help, they got the private key to DarkSide's BTC wallet
* Maybe can find a flaw in hackers' code
    - April 2021: QLocker ransomware flaw, glitch in payment system made it easy to look like a victim already paid, so the system unlocked the victim's file
    - In October 2021: NZ cybersecurity firm Emsisoft helped victims recover data
        - May 2021 Colonial Pipeline attack by DarkSide: group regrouped as new name BlackMatter
        - Emsisoft found error in BlackMatter's code that let it decrypt files and restore access to data
        - Secretly helped dozens of victims unlock data
        - Eventually BlackMatter caught on and patched the vulnerability, but Emsisoft saved millions of dollars in the meantime
* Controversy: FBI sat on Kaseya ransomware decryption key for 3 weeks instead of sharing it with victims of the REvil ransomware gang, costing them millions
    - Goal: not compromise FBI investigation, if nobody was paying up, REvil might get suspicious
    - Group shut down anyway first, went quiet for a while (then came back, got taken offline by US and others)
    - Unclear what victims should do, FBI says not to pay ransom but is not helpful when victims need it

## Foreign state-affiliated ransomware

### Russia
* Kremlin doesn't have broad control over private hacking groups, but leaves them alone as long as they don't hack the Kremlin and goals are broadly aligned with the Kremlin
    - Kremlin can send signals for the hacking groups to go underground; this happens if they believe US will retaliate with a cyberattack

### North Korea: Lazarus Group (APT 38) - December 2020 indictment
* Three individuals, members of units of RGB military intelligence agency
* Expansion of 2018 indictment of one of the three individuals
* Motive? "Further strategic and financial interests" of NK and Kim Jong Un
* Wide-ranging scope of acts: monetary gain, retribution, info-gathering, etc
    - Sony Pictures hack (2014)
    - Creating WannaCry 2.0 ransomware (2017)
    - Hacking banks + fraudulent wire transfers in 6 countries; >$1.3B (2015-2019)
    - Malicious cryptocurrency trading applications (2018-2020)
    - Stealing crypto from wallets of cryptocurrency companies (2017-2020)
    - ATM "cash-out": ATM dispenses cash to co-conspirator (2018)
    - Spear-phishing employees of state, DoD, defense contractors, energy utilities, etc
* Charges:
    - Conspiracy to violate the CFAA: sections 1030(a)(2)(C), 1030(a)(4), 1030(a)(5)(A), 1030(a)(7)(A)-(C) - similar to earlier ransomware indictments
    - Conspiracy to commit wire fraud and bank fraud
    - Interesting facts from indictment:
        - Hackers sometimes worked from China and Russia
        - Some of the theft for state, some for themselves
        - Names a Canadian co-conspirator sentenced to >11 years in prison for money laundering conspiracy, including 2 APT 38 heists

### China: Hainan MSS (APT 40) - May 2021 indictment
* 3 officers of Ministry of State Security; 1 hacker for hire
* Created a "security research" front company
    - Staffed and managed by local universities
* Another wide-ranging hacking scheme: steal IP and confidential data for competitive advantage of Chinese government, companies, and commercial sectors
    - Ebola virus/vaccine research
    - Submarine R&D
    - AVs
    - Chemical research
    - Genetic sequencing tech
    - Information on pending deals and disputes with governments of other countries (Malaysia, Cambodia)
* Hacked victims in 12 countries worldwide using spear-phishing
    - Research facility
    - Universities in multiple states
    - IT companies
    - Defense contractors
    - Swiss chemicals company
    - Airlines
    - Cambodian government ministry
    - Malaysian high-speed rail company
    - Malaysian political party
    - NIH
    - etc
* Stored malware and stolen data on GitHub, concealed using steganography, stored other stolen data on Dropbox
* Charges:
    - Conspiracy to violate CFAA, sections 1030(a)(2)(B)-(C), 1030(a)(5)(A)
    - Conspiracy to commit economic espionage under 18 U.S.C. section 1831
        - Same other law used in *United States v. Nosal*
* Note details: photos of the buildings where they worked, search queries they ran online (e.g., "Dropbox appkey"), passwords to malware and files
    - Anonymous research group "IntrusionTruth" reported in January 2020 on links between APT 40, Hainan MSS, and front company
* Overt acts alleged in furtherance of conspiracy occurred 2009-2018
    - Even after 2015 Obama-Xi agreement not to hack IP for commercial advantage
* APT 40 and ransomware
    - Allegedly, APT 40 engages in ransomware hacks now, not just for espionage
    - APT 40 blamed for MS Exchange email server hacking campaign in early 2021
        - Over 250,000 email servers compromised
        - Microsoft attribution in March 2021
        - White House attribution not until July, citing support from EU, UK, NATO
    - White House asserts APT 40 behind ransomware attacks
        - Alleges Chinese government turns a blind eye when MSS's current or former contract hackers conduct private ransomware attacks (not on state behalf)
    - White House stopped short of issuing sanctions on China
    - Why isn't Exchange/ransomware attacks in indictment?
        - Guess: Indicted individuals in APT 40 weren't behind those attacks
        - Possibly more indictments coming