# Demand paging
* Program can run without loading everything into memory
* Keep active info in memory
* Inactive info on disk in paging file/backing store/swap space

Locality: Most of the time, a process uses only a small fraction of its code and data
* Disk: 100x cheaper than DRAM
* DRAM: 100,000x faster than disk

Each page in VAS either
* in memory (physical page frame)
* on disk in backing store

## Page fault procedure
* Reference to page not in memory
* Present bit 0
* Trap to OS
* Find tree page
* Read data from backing store
* Set present bit to 1
* Resume execution

Technicalities:
* What address generated the fault?
    - Hardware register (`CR2` on x86)
* Restarting: Can we just restart instructions that failed?
    - Safe if instructions are idempotent (doing the same thing again yields same result)
    - For non-idempotent instructions (like `pushl %eax`): Hardware must track side effects and undo them on page fault

# Policies
1. Page fetching: when to bring page into memory?
2. Page replacement: which page to throw out?

## Demand fetching
* Start process with no pages in memory
* Page fault in when needed