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