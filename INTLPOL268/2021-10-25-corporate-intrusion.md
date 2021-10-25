# Corporate Intrusion

## Privilege escalation
* An attacker starts with no privilege and no ability to access anything
    - Initial entry point: sending a fake PDF to a lawyer, sending a fake Excel spreadsheet to an accountant, etc
* Attackers want to end with privileges necessary for their objectives
* Escalation may happen remotely or locally

### Computer privilege levels
1. Limited accounts (jails) - programs run with limited privileges to trap any attackers who successfully compromise them
2. Normal user account - ability to run programs and access data in home and shared directories; but cannot install software and access data from other users
3. Administrator/root - highest privileged user account, can access anything on the device but cannot necessarily modify OS itself
4. Service accounts - used by software that runs in the background, has lots of power but cannot modify running system
5. System/kernel - fully privileged to interact directly with hardware, access all data, modify running system

### Managed systems
* Computers on a corporate network are managed by a central authority (i.e., IT department) to provide a consistent environment
* Corporate endpoints (i.e., user laptops, servers, other equipment) connect to corporate domain server that manages endpoints

To exploit:
* Send payload (i.e., compromised Excel file or phishing email) to endpoint
* Example: attack via legal department, malware jumps from endpoint to domain server which can then attack more endpoints
* However, modern firewalls can do detection of data exfiltration

## Hashes

### What is a hash?
A hash is a function that creates a "fingerprint" of an arbitrary input that is
1. The same every time
2. Fixed length
3. Difficult to return to the original
4. Small change in the input leads to large change in the hash

### How to break hashes?
* Brute-force (alphabetical) attack
    - Computationally expensive, many strings unlikely to be passwords
* Dictionary attack: maintain dictionary of common passwords, check them all
* Can do password cracking with GPUs for speedup

## Eternal Blue
* Exploit engineered by NSA in Microsoft Windows SMB server (file sharing protocol for local servers)
    - Functionality was enabled for all Windows computers as late as 2017
* Leaked by hacking group Shadow Brokers in April 2017