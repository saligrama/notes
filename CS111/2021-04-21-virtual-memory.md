# Virtual memory
How to share one set of memory between multiple processes?

Goals:
* Multitasking: memory shared between processes running at the same time
* Transparency: user processes should not have awareness of shared memory
    - Process should see only its own private pool of memory
* Isolation: one process can't corrupt another
* Efficiency: don't want to add extra runtime to OS code or complexity to use code

## Load-time relocation
When loading process,
* Find memory region (using first-fit or best-fit)
* Relocate program 
    - Find all pointers in the program (linker identifies)
    - Add base
* Problems:
    - Fragmentation
    - Process's memory fixed at load time
    - No isolation/protection
    - Relocation can be slow
    - Can't move process once it starts executing