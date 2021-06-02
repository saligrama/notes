# Virtual machines
* "process" running in its own computer
* Can have many VMs on a single machine
* *hypervisor*: implements VM abstraction
* *guest OS*: runs in VM

## Hypervisors
* Simulator, use file to hold virtual disk and software emulation of CPU/memory oprations
    - Examples: Bochs, Emu
    - 100x slower for computation
    - 2x slower for I/O
* Use machine to simulate itself?
    - Guest OS in user mode
    - Most instructions execute natively
    - Trap and simulate "unusual" instructions

### Simulating an OS
1. Privileged instructions (i.e., `halt`)
    - Trap
    - If ok to execute, hypervisor itself simulates instruction
2. Kernel calls
    - When in hypervisor, PS bit set to `kernel`
    - Guest mode: PS bit set to `user`
    - User process executes a syscall, this is executed by hypervisor through guest OS
    - Then return from trap - another trap is called (due to illegal instruction, since PS bit needs to be changed)
    - Hypervisor transfers all info from the guest OS and returns it all to user process
3. I/O devices
    - Guest OS reads/writes device registers
    - Hypervisor arranges for page faults
    - Hypervisor simulates instruction, can determine read or write, address (device register), value for a write
    - Hypervisor simulates device
    - Hypervisor simulates interrupts
    - Can be slow: dozens of instructions to simulate
    - Paravirtualization: hypervisor provides I/O kernel calls, guest OS's have drivers

## Virtual memory
* Guest OS has its own virtual address space and physical address space with its own page maps
* Host OS has its physical and virtual address spaces
* Hypervisor page maps convert between the two
* Hypervisor extended page maps require processor support (Intel VT-x)
    - Previously: shadow page tables (real MMU page tables) that translate directly from guest virtual to machine physical

### Shadow page maps
* Guest sets page map base: trap
* Guest OS modifies page map: trap
* Page faults happen when:
    - "normal" way: hypervisor will make visible to guest OS
    - Guest physical page not in machine memory: hypervisor yields page, return (not visible to guest)
    - Page in memory, but shadow page table not up to date: hypervisor updates shadow page table from guest page map (not visible to guest)

### Dynamic binary translation
* Hypervisor analyizes all code
* Replaces non-virtualizable instructions with traps
* How to find all code?
    - Very complex process
* Result: able to virtualize instructions that were previously unvirtualizable

## Cost of VMs
Using best available techniques today:
    - CPU-bound: less than 5% slowdown
    - I/O-bound: ~30% slowdown

## History
* Originated late 1960s
    - One VM/user
    - Different OSs
    - Shared hardware
* Interest died in 1980s
    - Personal computers: everyone started to have their own computers
* Reinvented at Stanford: Mendel Rosenblum et al. founded VMWare
    1. Software development: test machines, can have consistent OS versioning or test on different OSes on the same hardware
    2. Datacenters: previously needed separate hardware for different applications (because Windows was flakey, so needed crash isolation), now could use the same hardware and consolidate with one VM per application. Also, could provision hardware separate from software
    3. Encapsulation, restart: save snapshots of VM in a file, restart later or rollback if something goes wrong, or migrate VMs to different physical machines
* Containers
    - Lightweight VMs
    - Run on existing OS, gives some virtualization facilities (apps can run in their own virtual file system)
    - Restrictions: All containers have to run same underlying OS