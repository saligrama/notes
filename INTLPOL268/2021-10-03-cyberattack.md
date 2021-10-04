# Anatomy of a Cyberattack

## Classifying cyber actors
* **What** can they do? (skill)
* **Who** are they? (attribution)
* **Why**? (goals)
    - Money
    - Political motivations
    - Anarchy

Leads to the question of: **How** can we deter them?

## Types of state cyber actors
* Superpowers: Five Eyes (US, UK, AU, CA, NZ); CH (Mandiant APT 1, APT 17); RU (Mandiant APT 28, APT 29); IL; FR, DE, NL (?)
    - Large, well-funded professional organizations
    - Full-spectrum operations including HUMINT
    - Advanced, self-driving malware with 0-days
    - Careful operational security (ability to not get caught)
* Rapid Risers: IR, KP, VE, SK
    - Rapidly improving via investment and foreign help
    - Often using cyber to level playing field
    - Might have 0-day, often new malware
    - Learn quickly from the superpowers
* The Peleton (IN, PK, SA, BR, TR)
    - Cyber capabilities seen as part of national power
    - Skilled, but perhaps smaller, teams
    - Often dependent on private groups, superpowres
    - Poised to break out with right investment
* Ambitious buyers (MX, ET, AE)
    - Purchasing both software and often operations
    - Limited in-house development
    - Likely to use cyber power domestically

## Nation-state control
* Most control: US, UK
    - Offensive operations under direct control with legal guidelines
    - Non-authorized hacking prosecuted
* More control: IN, SK
    - Pro-government operations carriedout by independent group with tight controls
* Mixture: CH, TR, IL
    - Mixture of first and third-party operations
    - Independent groups allowed to operate but tightly controlled
* Less control: RU, VE
    - Mixture of first and third-party operations
    - Independent groups encouraged to go rogue, internal politics can be dangerous
* Lawless: NG, RO
    - Hackers operate for pure profit motive, government cannot/will not intervene

## Cyber Kill Chain
1. Reconnaissance: Planning phase of operation; research on targets
2. Weaponization: Preparation and staging phase of operation; automated generation of malware; weaponizer couples malware and exploit into a deliverable payload
3. Delivery: Adversaries convey malware to the target
4. Exploitation: Adversaries must exploit vulnerability to gain access (0-days)
5. Installation: Adversaries install persistent backdoor in victim environment to maintain extended access
6. Command and Control (C2): Malware opens a command channel to enable adversary to remotely manipulate the victim
7. With hands-on keyboard access, intruders accomplish the mission's goal

Social engineering is much simpler: can accomplish goals (information theft) with only first 4 steps; phishing payload is easy to create.