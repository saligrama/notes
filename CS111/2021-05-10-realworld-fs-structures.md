# DOS/Windows FAT
* Linked list: links in File Allocation Table
* One entry per block
    - Next block in file
    - "Last block"
    - "Free block"
* Inode: first block in file
* Free list: like a bit map
* Originally: 16-bit entries, 512 byte blocks => 32 Mb disks

Advantages:
* Random access is fairly fast
* Sequential access is easy
* FAT itself is free list!
* No pointers in data block
* Contiguous allocation is *possible*

Disadvantages:
* Fragmentation issues (but better than linked files)

## FAT32
* 28-bit sector numbers
* Clusters: groups of sectors, 2-32kb
* 4kb clusters: 1TB disks

# Multi-level indexes (4.3BSD Unix)
* 4 KB blocks
* inode: 14 pointers (kept in an array)
    - First 12 pointers are direct (to block)
    - 13th pointer is indirect, points to a structure with 1024 pointers to blocks
    - 14th pointer is doubly indirect, points to a structure with 1024 block pointers, each again with 1024 block pointers

Advantages:
* Organize files for faster access
* Bounded random access time
* Don't need to predeclare file size, files can grow
* Small files have fast access
* Sequential accesses are easy

Disadvantages:
* Non-uniform random access times (up to 3 I/Os)
* Upper limit on file size

# Block cache
* Cache recently-used disk blocks in memory
* Use some form of LRU (least-recently-used) replacement
* Helps with random access time, don't need to pay high I/O cost

Caches today get very large (up to 1GB or more!), can vary in size

# Modifying blocks
* Write through
    - Safe (data written to disk, won't get lost in crash)
    - Slow
* Delayed writes
    - Write after 30 seconds
    - Fast
    - Often, no I/O is needed! (repeated writes, short lifetimes)
    - Dangerous: can lose data after crash

# Free space management

## Bit map
* 1 bit/block
* 0: free, 1: in use
* 1TB disk, 4kb blocks: bitmap is 2^25 bytes = 32mb

Advantages:
* Gives good locality for expanding files
* Works well if disk isn't full - can get fast contiguous allocation

Disadvantages:
* If disk full
    - Needs lots of searching to find another block
    - Lose locality

To keep the filling up, lie to the user about free space left
* Show the user their disk is 100% full at 90% full

# Block sizes
* Originally, 512b blocks
    - More seeks
    - Space inefficient: ~1% of disk for pointers
* 4kb blocks
    - Fewer seeks
    - Less overhead
    - More internal fragmentation, wasted ~50% of disk
* 4.3BSD: hybrid
    - Large blocks: 4kb
    - Fragment: last block of a file could be 0-7 512kb chunks
    - Bit map based on fragments
    - Somewhat slow

## Misc strategies for efficiency
* Larger blocks: 16kb large blocks, 2kb fragments
    - Fine because disks are larger now
* Reallocate files as they grow
    - Initially allocate blocks one at a time
    - Large files: reallocate contiguously (maybe reserve certain areas of the disk for large files)
* Delay allocation
    - Not until cache flush
    - Allocate clusters with more info