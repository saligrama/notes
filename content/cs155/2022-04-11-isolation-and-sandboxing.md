# Isolation and Sandboxing

Goal: run untrusted code without compromising systems

* Programs from untrusted Internet sites
    - Mobile apps, JS, browser extensions
* Exposed applications: Browser, PDF viewer, email client
* Legacy daemons: sendmail, bind
* Honeypots
* If application misbehaves, want to kill it

# Approach: confinement

* Idea: ensure misbehaving app cannot harm rest of system
* Can be implemented at many levels
    - Hardware: run application on isolated hardware (airgap) - difficult to manage
    - Virtual machines: isolate OS's on a single machine
    - Process level: system call interposition; isolate a process in a single OS
    - Threads: software fault isolation (SFI)
        - Isolating threads sharing same address space
    - Application level confinement
        - e.g. browser sandbox for Javascript and WebAssembly

## Implementation

Key component: reference monitor

* Mediates requests from applications
    - Enforces confinement
    - Implements a specified protection policy
* Must *always* be invoked; every application request must be mediated
* Tamperproof: reference monitor cannot be killed; if it is, then monitored process is also killed

## Example: `chroot` jail

To use (must be root):

```bash
chroot /tmp/guest
su guest
```

* Root dir `/` is now `/tmp/guest`
* EUID set to `guest`
* Now `/tmp/guest` is added to every file system access
    ```c
    fopen('/etc/passwd', 'r'); // becomes the below:
    fopen('/tmp/guest/etc/passwd', 'r');
    ```
* `chroot` should only be executable by `root`, otherwise jailed app can do
    - Create dummy file `/aaa/etc/passwd`
    - Run `chroot /aaa`
    - Run `su root` to become `root`
    - Many ways to escape jail as `root`:
        - Create device that lets you access raw disk
        - Send signals to non-`chroot`ed process
        - Reboot system
        - Bind to privileged ports

### FreeBSD jail

* Stronger mechanism than simple `chroot`
* To run: `jail [jail-path] [hostname] [IP-addr] [cmd]`
    - Calls hardened `chroot` (no `../../` escape)
    - Can only bind to sockets with specified IP addresses and authorized ports
    - Can only comunicated with processes inside jail
    - Root is limited, e.g. cannot load kernel modules

### Problems with `chroot` jails

* Coarse policies: all or nothing access to parts of a file system
    - Inappropriate for apps like web browsers, which need access to files outside jail (e.g. for sending attachments over email)
* Does not prevent malicious apps from
    - Accessing network and messing with other machines
    - Trying to crash host OS

## System call interposition

* Observation: to damage host system (e.g. persistent changes), app must make system calls
    - To delete/overwrite files: `unlink`, `open`, `write`
    - To do network attacks: `socket`, `bind`, `connect`, `send`
* Idea: monitor app's system calls and block unauthorized calls
* Implementation options
    - Completely kernel-space (e.g., Linux `seccomp`)
    - Completely user-space (e.g., program shepherding)
    - Hybrid (e.g. `systrace`)

### Early implementation: Janus (1996)

* Linux `ptrace`: process tracing
    - Process calls `ptrace(..., pid_t pid, ...)` and wakes up when `pid` makes system call
* If monitored process calls `fopen`, monitor decides if application is allowed, if not, monitor kills application
* Example policy (e.g., for PDF reader)
    ```
    path allow /tmp/*
    path deny /etc/passwd
    network deny all
    ```
* Manually specifying policy for an app can be difficult
    - Recommended default policies are available, can be made more restrictive as needed
* Complications
    - If app forks, monitor must also fork; forked monitor monitors forked app
    - If monitor crashes, app must be killed
    - Monitor must maintain all OS state associated with the app
        - Current working directory, UID, EUID, GID
        - When app does `cd path` monitor must update its CWD
    - Problems with `ptrace`:
        - Trace all system calls, which can be inefficient: no need to trace `close` system call
        - Monitor can abort syscall without killing app
    - Race conditions (time of check/time of use  - TOCTOU bug) since checking/opening is not atomic
        1. Proc 1: `open("me")`
        2. Monitor checks and authorizes
        3. Proc 2: symlink `me` to `/etc/passwd`
        4. OS executes `open("me")`

### SCI in Linux: `seccomp-bpf`

* `seccomp-bpf`: Linux kernel facility used to filter process syscalls
    - Syscall filters written in BPF language (use BPFC compiler)
    - Used in Chromium, Docker containers, etc.
* How this works:
    1. Chrome renderer process starts
        ```c
        prctl(PR_SET_SECCOMP, SECCOMP_MODE_FILTER, &bpf_policy)
        ```
    2. Renderer process renders site
    3. If due to exploit the process tries to do
        ```c
        fopen("/etc/passwd", "r");
        ```
        then `seccomp-bpf` will kill process

#### BPF filters (policy programs):
- Process can install multiple BPF filters
- Once installed, filter cannot be removed - all run on every syscall
- If process forks, child inherits all filters
- If program calls `execve`, all filters are preserved
- BPF filter input: syscall number, syscall args., system architecture
- Filter returns one of
    - `SECCOMP_RET_KILL`: kill process
    - `SECCOMP_RET_ERRNO`: return specified error to caller
    - `SECCOMP_RET_ALLOW`: allow syscall

#### Installing a BPF filter

* Must be called before setting BPF filter
* Ensures `setuid`, `setgid` ignored on subsequent `execve`
    - Attacker cannot elevate privilege

```c
int main (int argc, char **argv) {
    prctl(PR_SET_NO_NEW_PRIVS, 1);
    // kill if call open() for write
    prctl(PR_SET_SECCOMP, SECCOMP_MODE_FILTER, &bpf_policy);
    fopen("file.txt," "w");
    assert(false); // should never reach here
}
```

#### Docker: isolating containers using `seccomp-bpf`

Container: process-level isolation

* Container prevented from making syscalls filtered by `seccomp-bpf`
* Whoever starts container can specify BPF policy
    - default policy blocks many syscalls, including `ptrace`
* e.g. Nginx: `docker run --security-opt="seccomp=filter.json" nginx`
    ```json
    "defaultAction": "SCMP_ACT_ERRNO." // deny by default
    "syscalls": [
        {
            "names": ["accept"], // syscall name
            "action": "SCMP_ACT_ALLOW", // allow (whitelist)
            "args": [] // what args to allow
        },
        // ...
    ]
    ```
* More Docker confinement flags:
    - Specify an unprivileged user:
        - `docker run --user www nginx`
    - Limit Linux capabilities: drop all capabilities, allow bind to privileged ports:
        - `docker run --cap-drop all --cap-add NET_BIND_SERVICE nginx`
    - Prevent process from becoming privileged (e.g. by a `setuid` binary)
        - `docker run --security-opt=no-new-privileges:true nginx`
    - Limit number of restarts and resources
        - `docker run --restart=on_failure:$MAX_RETRIES --ulimit nofile=$MAX_FD --ulimit nproc=$MAX_NPROC nginx`


## Confinement via virtual machines

About VMs: [see my notes from CS 111](../CS111/2021-05-28-virtual-machines.md)

### Hypervisor security assumption

* Malware can infect  *guest* OS and guest apps
* But malware cannot escape from the infected VM
    - Cannot infect host OS or other VMs on the same hardware
* Requires that hypervisor protects itself and is not buggy
    - Some hypervisors are much simpler than a full OS
### Problem: covert channels

* Unintended communication channel between isolated components
* Can leak classified data from secure component to public component
* Example communication between cooperating malware and listener
    - To send a bit, malware does
        - `b = 1`: at 1:00am, do CPU intensive calculation
        - `b = 0`: at 1:00am, do nothing
    - At 1:00am, listener does CPU intensive calculation and measures completion time
        - `b = 1`: completion time > threshold
- Many covert channels exist in running system
    - File lock status, cache contents, interrupts, etc.
    - Difficult to eliminate all
* VMs from different customers may run on same machine in the cloud
    - However, some data may leak

### VMs in end-user environments 

* Qubes OS: a desktop/laptop OS where everything is a VM
    - Runs on Xen hypervisor
    - Access to peripherals (mic, camera, USBs, ...) controlled by VMs
    - Every window frame identifies VM source

### Hypervisor detection

* Malware can detect hypervisor and refuse to run to avoid reverse-engineering
* Software that binds to hardware can refuse to run in VM
* DRM systems may refuse to run on top of hypervisor
* How to detect?
    - VM platforms often emulate simple hardware (e.g. i440bx chipset) but reports 8GB RAM, dual CPUs, etc
    - Hypervisor introduces time latency variances
        - Memory cache behavior differs in presence of hypervisor
        - Results in relative time for any two operations
    - Hypervisor shares TLB with guest OS
        - Guest OS can detect reduced TLB size
    - Can do detection in the browser
        - Timing variations in writing to the screen
        - Disadvantages identification of malware websites using VMs
* The perfect hypervisor does not exist
    - Hypervisors today focus on compatibility (ensuring off-the-shelf software works) and performance (minimizing virtualization overhead)
    - VMMs do not provide transparency - anomalies reveal existence of hypervisor