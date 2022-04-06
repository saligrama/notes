# Principles of secure systems

## Inevitability of vulnerabilities

* Very easy to make mistakes involving buffer overflow, use-after-free, or null pointer dereferences
    - Many such mistakes can allow attackers to run malicious code
* As such, there will *always* be bugs, even as we get better at finding and preventing them
* Systems must be designed to be resilient in the face of both software vulnerabilities and malicious users

## Defense in Depth

* Systems should be built with security protections at multiple layers
* e.g. in the case of a vulnerability in Chrome Javascript interpreter:
    - Chrome should prevent malicious website from accessing other tabs
    - OS should prevent access to other processes (e.g., password manager)
    - Hardware should prevent permanent malware installation in device firmware
    - Network should prevent malware from infecting nearby computers

## Principle of least privilege

* Users should only have access to the data and resources to perform routine and authorized tasks
* This helps protect against
    - Malicious or curious owners of overprivileged accounts doing widespread damage
    - External attackers causing widespread damage simply by owning a poorly secured overprivileged account
* Privilege separation: dividing a system into parts to which we can limit access
    - Segmenting a system into components with least privilege needed can prevent an attacker from taking over the entire system

### Privilege separation

### Security subjects and security policies

#### Security subjects

* Unix: a **user** should only be able to read their own files
* Unix: a **process** shouuld not be able to read another process's memory
* Mobile: an **app** should not be able to edit another app's data
* Web: a **domain** should only be able to read its own cookies
* Networking: only a trusted **host** should be able to access a file server

#### Security policies
* Subject (who?): acting system principals (e.g. user, app, process)
* Object (what?): protected resources (e.g., memory, files, hardware devices)
* Operation (how?): how subjects operate on objects (e.g., read, edit, delete)
* Example policies:
    - Unix: a **user** should not be able to **delete** other users' **files**
    - Unix: a **process** should not be able to **read** another process's **memory**
    - Mobile: an **app** should only be able to **edit** its own **data**
    - Web: a **domain** should not be able to **read** another domain's **cookies**

### Privilege and access control in Unix

* Subjects: users, processes
    - Many user accounts: both service accounts for background processes, and user accounts tied to specific humans
    - Every user has a unique integer id (UID)
    - UID 0 is reserved for special user `root` that has access to *everything*
        - Many system operations can only run as root
    - User information is stored in `/etc/passwd`:
        ```
        root:x:0:0:root:/root:/bin/bash
        www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
        systemd-resolve:x:101:103:,,,:/run/systemd/resolve:/usr/sbin/nologin
        asaligrama:x:1000:1000:Aditya Saligrama:/home/asaligrama:/usr/bin/zsh
        ```
    - Becoming `root`:
        - `sudo`: run a single command as `root` (requires you to be listed in `/etc/sudoers`)
        - `su`: allows you to become root by knowing its password
        - `sudo su`: become root without knowing its password
    - Groups: collection of users who can share files and other system resources; every user has group ID (GID) and name
        - Info is stored in `/etc/group`
    - Processes are isolated and cannot access each other's memory
    - Processes run as a specific user
        - When you run a process, it runs with your UID's permissions
        - Process can access any files that the UID has access to and have same permissions as that UID
        - Processes started by root can reduce their privileges by changing their UID to a less privileged UID
            - Effective UID (EUID): determines permissions for process
            - Real UID (RUID): determines user that started the process
            - Saved UID (SUID): EUID prior to change
            - Changing user IDs using `setuid(x)` (syscall):
                - `root` can change EUID/RUID/SUID to arbitrary values
                - Unprivileged users can change EUID to only RUID or SUID
* Objects: files (sockets, pipes, hardware devices, kernel objects, process data), directories
    - All Unix resources are managed as files
    - All files and directories have a single user owner and group owner
    - SETUID bit: allows you setting EUID of an executable to be the file owner rather than the executing user
* Operations: read, write, execute

This is a simple system, but it isn't amazingly well thought out

* Privileged processes (EUID == 0) bypass all kernel permission checks, while unprivileged processes are subject to full permission checking
* Utilities like `ping` depend on `setuid`
* Very dangerous: a bug in many utilities can lead to full compromise

### Access control lists

* Generically: the above is a form of an Access Control List (ACL)
    - Every object has an ACL that identifies what operations subjects can perform
    - Each access to an object is checked against that object's ACL
    - Role Based Access Control (RBAC): genericization of ACL, with larger numbers of subjects, objects, and operations

### Privilege and access control in Windows

* Flexible ACLs
    - Objects have full ACLs: possibility for fine-grained permissions
    - Users can be members of multiple groups, groups can be nested
    - ACLs support allow and deny rules
* Every object has a security descriptor specifying who can perform what and who can audit rules, containing:
    - Security identifiers (SIDs) for owner and primary group of an object
    - Discretionary ACL (DACL): access rights allowed users or groups
    - System ACL (SACL): types of attempts that generate audit records
* Tokens (security context) for every process; OS checks these tokens for accesses:
    - ID of user account
    - ID of groups
    - ID of login session
    - List of OS privileges held by user/groups
    - List of restrictions
    - Impersonation token can be used temporarily to adopt a different context
* Windows Vista introduced concept of integrity levels
    - Untrusted
    - Low
    - Medium
    - High
    - System
    - Most processes run at medium level
    - Low level has limited scope; e.g. can read but not write files
* Capabilities vs ACLs
    - Capabilities: subject presents an unforgeable ticket that grants access to a subject
        - System does not care who subject is, just that they have access
    - ACLs: system checks where subject is on list of users with access to the object
        - Relying on user permission provides user with little protection against malicious applications
        - Malicious application running as you has access to all your files
        - Remedies: 
            - macOS app sandbox: sandboxes applications and mediates access to resources
            - Android process isolation: on Linux-based platform; each application runs with its own UID in its own VM
                - Apps cannot interact with each other
                - Limits access to system resources - decided at installation time

### Chrome security architecture

* When Chrome starts, it spins up a number of processes responsible for only one function
    - Network
    - Browser: controls "chrome" part of application like address bar, bookmarks; also handles invisible and privileged parts of a browser like network requests
    - UI
    - Storage
    - GPU
        - Handles GPU tasks in isolation from other processes
    - Device
    - Renderer
        - Controls anything inside of website tabs
    - Plugin
        - Controls plugins used by website (e.g. Flash)
* Communication between these processes happens through a set of hardened APIs
* On Windows: Chrome creates extremely restricted token that removes access to nearly every system resources
    - As long as disk root directories have non-null security, no files (even with null ACLs) can be accessed
    - Renderer runs as a "Job" object rather than an interactive process, this eliminates access to
        - Desktop and display settings
        - Clipboard
        - Creating subprocesses
        - Access to global atoms table
    _ Further isolation through putting target processes on additional virtual desktops
* Even further isolation by running separate copies of these processes based on site origin

## Open design

* "The secrecy of a mechanism should not depend on the secrecy of its design of implementation"
    - If the details of a "security-through-obscurity" system are leaked, then it is a catastrophic failure for all users
    - If the secrets are abstracted from the mechanism (e.g. per-user keys), then leakage of a key only affects one user
* In cryptographic world: called Kerckhoff's Principle
    - "A cryptosystem should be secure even if everything about the system, other than keys, are public knowledge"