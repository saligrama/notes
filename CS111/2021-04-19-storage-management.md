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