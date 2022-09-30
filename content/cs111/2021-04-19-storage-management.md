# Dynamic storage management

Problem: unpredictability

## Operations:
* `alloc(nbytes) -> ptr`
* `free(ptr)`

## Stack allocation
* Freeing is predictable: just LIFO order!

## Heap Allocation
* Freeing is unpredictable
* Memory has allocated areas and free areas ("holes")

Fragmentation: Small holes, can't effectively use for allocation

Goal: Minimize fragmentation

### Free lists
Data structure that keeps track of holes

#### Best fit
* Linked list of free blocks
* On allocation, scan entire list and pick the smallest block that can fit requested size
* On free, try to merge a block with its neighbors

#### First fit:
Stop at first block that is large enough

#### Bit map
* Good for fixed-size chunks
* Keep a long bit string with each bit referring to a fixed chunk of memory, with 1 as allocated and 0 as free

#### Slabs
* Keep pools of different sizes of memory
* Slab: large chunk of memory divided up into equal size fragments
* Each slab has own free list (unsorted)

Allocate:
* Find slab corresponding to user-requested size
* Return the first fragment from the free list
* If nothing free: allocate new slab

Free:
* Add the freed fragment back to the slab's free list
* If entire slab is free: free the whole slab

### Storage reclamation
* When to free?
    - Easy for simple structures: when we're "done using" an object, can free it
    - Shared structures: Free when there are no more uses (no remaining pointers to it)
* Problems
    * Dangling pointer: pointer exists to some memory, but that memory has been freed already
    * Memory leak: no pointers to memory, but haven't freed it

#### Reference counting
* For each object, keep track of the number of pointers to it
* When there are no pointers left to the object, free it
* Problem: circular references cause memory leak

In C++: `std::shared_ptr`

#### Garbage collection
* No free! Just delete pointers
* When system detects it is running low on memory, runs the garbage collector
    - Scan storage and pointers
    - Finds all non-reachable objects and frees them
    - Compacts heap and reduces fragmentation


##### Mark and Copy
* Must be able to find all objects
* Must identify all pointers

Pass 1: Marking
* Starts at "roots"
    - Objects on stack
    - Static objects
* Mark each found object
* Find pointers in object
* Mark recursively

Pass 2: Copying and Compacting
* Scan memory for all objects
* If the object is live (mark bit set), copy it down to the bottom part of memory
    - Update pointers to live objects
* When done, all extra space is freed

Problems:
* Expensive! Takes 10-20% of execution time
* 2-5x memory overallocation
* Long pauses: sometimes at least 100ms