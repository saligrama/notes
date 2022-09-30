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

## Page prefetching
* Load in before page fault
* Requires predicting the future
* One possible scheme: After page fault, read successive pages in addition to the one that was requested
* Cost is cheap:
    - First 4kb page: 5-10ms
    - Additional pages: 0.4ms

## Page replacement
Which page to remove from memory?
* Random
* FIFO: replace oldest page in memory
* LRU (Least recently used): Page whose most recent access was farthest in the past
* Minimize pagefaults (optimal): page whose next reference is farthest in the future
    - Requires us to predict the future

### Implementing LRU
* Perfect LRU would require hardware support (one register per page, store current clock into the page)
* Approximation: find *some* old page
    - Also requires hardware support, but simpler
    - Two extra bits per page map entry (referenced and dirty)
    - Referenced bit set when page accessed
    - Dirty bit set when page written to

### Clock algorithm (Second chance)
* Consider each page in memory as positioned around the edge of a clock
* Hand on the clock points to a page at any given time
* Hand sweeps clockwise, scanning pages circularly
* On page fault:
    - Advance hand
    - If current page has been referenced, clear referenced bit and continue
    - If page has not been referenced, replace that page
* Hand moving slowly? Not many page faults, which is good
* Hand moving quickly Many page faults, memory might be too small

#### Global replacement
* One memory pool for all proceses
* One process can evict from other processes
* One pig hurts everyone

This policy is mostly used in real-world systems.

#### Per-process replacement
* Each process has separate memory pool
* Only replacement from same process pool
* Hard to figure out how much memory to allocate to each process

#### Thrashing
* Overcommitted memory
* Each page fault replaces active pages
* System only gets work done at the rate of the disk

To fix, stop running some programs (move their memory to disk)

With PCs, user will notice and either kill processes or buy more RAM