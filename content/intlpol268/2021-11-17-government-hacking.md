# Government Hacking

Specifically: governments hacking individuals for law enforcement purposes

Tools in law enforcement's investigative toolbox (Easy to Hard)

* Traditional evidence-gathering: warrants, ECPA, etc: Evidence is readily available
* Compelled technical assistance: Make provider repurpose a product feature for law enforcement purposes
* Government hacking: Using a bug for law enforcement purposes without provider participation

## Overview

* Usual authority: search and seizure warrant (Fed. R. Crim. Pro. 41)
    - Maybe no warrant needed if LE is only collecting IP address, according to multiple courts (have no reasonable expectation of privacy in IP address)
        - State law may compel: Cal ECPA requires LE to get warrant for IP addresses
    - Since Dec. 2016: statute expressly allows remote-hacking warrants
* Purposes of hacking
    - Discover suspect's locaton and identity (i.e., IP address)
    - Seize evidence stored on computer or phone
    - Collect evidence going forwards
        - Implant software on device that collects info (e.g., keylogger)
    - Disable functionality that may impede evidence-gathering if computer is seized later (e.g. full-disk encryption)

## Old school: keystroke loggers

Early "government hacking" case: *U.S. v. Scarfo* (D. N.J. 2001)

* FBI developed keylogger; details classified for national security purposes
* FBI got warrant to search Scarfo's office and computer in early 1999
* Computer files of interest to FBI were encrypted
* FBI got court order allowing keylogger on computer keyboard to capture Scarfo's decryption password as he logged in
* Required physical entry; today keyloggers can be remotely installed
* Configured not to activate if modem was in use (due to Wiretap Act)

## More recent: digital forensics tools to access devices

* FBI has own in-house hacking R&D units
    - Operational technology division (OTD) Digital Forensics and Analysis Unit
* Third party vendors of tools that use vulns to extract data off locked phones
    - Cellebrite (Israel): Universal Forensic Extraction Devices (UFED)
        - iOS, Android, BlackBerry
        - Cellebrite's tools and services have had own vulns in the past
    - Grayshift (U.S.): GrayKey devices, iOS only
* Customers: federal, state, local LE agencies; military
* Oct. 2020 Upturn report: 2000+ LE agencies in all 50 states have these tools
    - Used for minor crimes as well as serious ones
* Sharing access: fed/state partnerships (e.g. Regional Computer Forensics Labs)
* Perennial cat-and-mouse games between vendors and Apple/Google/etc
* Undermines LE arguments for mandatory encryption backdoors for devices
    - Shift from "can't get into encrpyted phones at all" to "can't get in quickly"

## Hacking computers remotely

### In-house Hacking R&D:

* Intelligence
    - NSA: Tailored Access Operations (now called Computer Network Operations)
    - CIA: hacking devision within Center for Cyber Intelligence (CCI)
* Law enforcement
    - FBI -> OTD -> Technical Surveillance Section -> Remote Operations Unit (most information about this is classified)

### Old school: CIPAV (Computer and Internet Protocol Address Verifier)

* FBI-developed tool, used at minimum 2004-2007
* FBI obtained search warrants authorizing use of CIPAV where targets used anonmyizing services to hide ID and location
* Deployed by sending phishing messages to specific targets
* CIPAV collected computer's true IP address, MAC address, logged-in user name, last URL visited, other metadata
* Transmitted data back to FBI via internet

### More recent: EncroChat

* Secure phone companies sell to both paranoid people and crime rings
    - Encrypted device (typically modified Android handset), encrypted video/voice/text/email, extra security features, etc
    - Examples: Blackphone (created by PGP co-founders), Phantom Secure (started legit but founder now in prison, refused FBI request to install backdoor), MPC (run directly by organized criminals), Ciphr (popular with criminals, allegedly has stopped operating in US and AU)
* EncroChat: secure phone of choice for much of Europe's drug trafficking and organized crime
* 3-year joint French and Dutch sting operation culminating in June 2020
    - French authorities took over an EncroChat server housed in France
    - French/Dutch authorities used the compromised server to deploy malicious update to all devices
    * Could thus record users' lockscreen passcodes, identify wifi hotspots near the devices (and thus location), and MITM traffic passing over the EncroChat network (>100M messages)
* French/Dutch team shared data with other countries: 800+ arrests in 5+ countries
* Was this mass hacking operation legal?
    - French relied on law allowing use of surveillance tools without consent
    - In requesting data, UK authorities claimed most/all users were criminals and there were no legit uses for the phone
    * UK challenge failed in English high court, challenge in FR supreme court pending after failing in appeals court, other legal challenges in DE, NL, SE

### Anom (Operation Trojan Shield)
* Arose from ashes of Phantom Secure (shut down 2018), operation ran from March 2018 to June 2021
* FBI cultivated former Phantom Secure reseller (CHS) to develop new "secure" phone and resell to criminals
* FBI + AU federal police (AFP) worked with CHS to code a system where messages could be read by AFP
* Anom built up a marketing profile, leveraged CHS's networks to gain trust of phone resellers and criminal clients; AFP did a test run of ~50 devices, sales grew by word of mouth to ~12K devices in 9o0 countries; FBI and AFP allege that 100% of users were using devices for criminal purposes
* 20M+ messages intercepted, AFP and unknown third country shared users' communications with FBI
* FBI spied on non-US persons, laundered its spying on US persons via other countries' surveillance laws
    - FBI excluded US devices via geo-fencing, so FBI didn't access any US-based comms
    - For devices in US (only ~15 out of 100s), AFP monitored comms instead
    - For non-US fones, encrpyted "bcc" of each message was routed to server outside US, then decrypted, then re-encrypted with FBI key, passed to FBI-owned server, then decrypted and ready to view
    - Lengthy negotiations about legal framework between FBI and third country - third country got court order under its law to copy an Anom server there and provde periodic copies to FBI 2-3x/week under MLAT, without reviewing content itself
* FBI's near term goal: infiltrate criminal networks; larger goal: shake confidence in "secure" phone industry

### NSO Group
* Private-sector vendor, like Cellebrite and Grayshift
* Provides remote-hacking software to governments, tech support, training
    - NSO claims only used for LE and CT purposes
    - But Pegasus software is used to hack activists, dissidents, journalists, etc
    - Summer 2021 report: leaked list of potential targets, 50k+ phone numbers
* Nov. 2021: added to Commerce Dept. Entity List due to enabling these abuses
    - Related: new Commerce Dept. rule effective Dec. 2021, barring selling hacking tech to RU/CN/etc (pursuant to Wassenaar Arrangement)
* In Oct. 2019, Facebook sued NSO over WhatsApp hack
    - NSO allegedly built and sold a hacking platform that exploted a flaw in WhatsApp servers to help clients hack WA users' phones
    - NSO created WA user accounts, used them to send malicious code to target devices - "zero-click attack"
    - Novel CFAA theory: Hacking WA users = CFAA violation against WhatsApp
        - NSO hacking tool was used to hack users but using WA equipment
        - Users could have sued for CFAA violations on their own behalf, too
    - At least 1400 WA users were affected and notified (including 100+ journalists, human rights activists, lawyers, etc)
        - WA checked for LE demands first before notifying them (to mitigate investigatory impact)
    - Jul. 2020: District court denied NSO motion to dismiss CFAA claim
        - Accounts had rights to access parts of WA (no "without authorization")
        - But NSO's code used WA's relay servers without authorization and evaded technical restrictions on access to "signaling servers" ("exceeds authorized access")
    - District court rejected NSO's "derivative foreign soverign immunity" theory
        - Doctrine does not apply to foreign contractors of foreign sovereigns
    - Nov. 2021: Ninth Circuit upheld that rejection, denied NSO appeal
    - Case now goes back to district court unless SCOTUS to take it
    - Compare to Kidane v. Ethiopia (D.C. Cir. 2017)
        - Ethiopian-born US citizen living in US, hacked by Ethiopia with FinSpy software
        - Lawsuit barred by FSIA because hacking conducted of Ethiopia, FSIA requires all elements of wrongdoing to occur within the US

### Attempts to use hacking tools by LEAs

* DEA: has bought or considered buying surveillance tools from controversial companies
    - Discussions with NSO group in 2014, decided NSO tools were too expensive
    - Bought tools from reseller of Italian surveillance-tech firm Hacking Team in 2012-2015
* FBI: pose as journalist strategy was used by Iranian govt hackers in 2019-2020
    - Impersonated journalists -> gained targets' trust -> sent them infected files, phishing links
    - Targets: academics, human rights, journalists

**When repressive governments use same tools as US, harder for us to condemn it**

* Increased inter-departmental cooperation on hacking (DHS, ICE, Secret Service)
* DOJ: has given PPT trainings on the use of NITs to staff at federal agencies
* IRS and ICE: possible users
    - IRS may be using exploits in Adobe software to deliver malware
    - ICE, Secret Service have been emailing about govt use of malware
* Secret Service
    * 2018: internal discussions of legality and practicality of using hacking tools
    * Late 2016/early 2017: Seattle PD officer, while detailed to a SS task force, unsucessfully tried to use an NIT to unmask a Tor-using ransomware attacker

**Govt use of hacking tools continues to expand, need oversight/transparency/regulation**