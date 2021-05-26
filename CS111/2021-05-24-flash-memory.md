# Flash memory

Flash vs disk:
* No moving parts
* More shock resistant
* 100x lower latency
* 5-10x higher cost per bit

Flash vs DRAM:
* Nonvolatile
* 5-10x lower cost per bit
* 1000x higher latency

Hardware:
* Individual chips can be up to 512GB
* Erase units: 256kb
* Pages: 512 or 4096 bytes
* Reads: pages
* Writes:
    - Erase: sets all bits in an erase unit to 1
    - Writes: pages: logical AND with data written and data existing (0s never change back to 1)
* Wear-out: limit on how many erases can be invoked without degrading reliability (1k-100k)

Performance:
* Reads: 20-100 microsecond latency, 100-2500MB/sec
* Erasure: 2ms
* Writes: 200 microsecond latency, 100-500MB/sec

## Flash translation layer (FTL)
* Software layer built into drive
* Mimics disk interface
* Works with existing software

Disadvantages:
* Sacrifice performance
* Waste capacity
* All are proprietary!

### Direct mapped
* Virtual block == physical block
* Reads are obvious
* Writes:
    - read erase unit
    - erase
    - rewrite with new block
* Used with FAT format

Disadvantages:
* Any write gets closer to wear-out limit
    - Especially with frequently accessed blocks
* No way to handle "hot block"
* Slow
* Brittle: loses data in crashes
    - Including OLD data!

### Virtualization
* Separate virtual/physical block numbers
* A virtual block can move in flash
* Block map: 
    - index = virtual block number
    - value = physical block corresponding to virtual block
* Reads: look up in block map, read corresponding physical block
* Writes:
    - Find free/erased page
    - Write virtual block to that page
    - Update block map
    - Mark old page as free

One possible scheme:
* Don't store map on device
* Page header:
    - Virtual block number
    - Allocated bit (1: free, 0, allocated)
    - Written (1: clean, 0: dirty)
    - Garbage (1: usable, 0: garbage/need to erase)

Another scheme:
* Block map on disk, cache in memory
* Page header bit: "data" or "map"
* Keep map-map in memory
* Reconstruct map-map on startup
* Writes: must write map as well as data
* Reads: could take 2 reads

### Garbage collection
* Keep track of pages that are useful vs garbage
* Pick erase units with many garbage pages
* Read all live pages and write into fresh erase units
* Erase old unit

Write costs:
* Suppose 90% utilized
* 10 erase units
* Read 9 erase units, write 9
* 1 free erase unit
* Requires 19 I/O's: *write amplification*

Formula: cost = (1+U)/(1-U) where U is utilization (0.0-1.0)

Ideal:
* Hot (short-lived) and cold data
* Want to segregate: most storage space is cold data, small amount hot
* Run cold storage at high utilization, pay GC cost if there's free space in cold erase units
* Hot storage at low utilization

### Wear level
* Erase at same rate everywhere
* Use GC to move blocks between hot and cold pages
* Occasionally: copy data from cold unit to hot one

### Disadvantages of virtual disks
* Duplication of FTL block map, FS inode
* Missing info:
    - FTL doesn't know about deletions
    - GC copies live blocks!
    - To fix, new command *trim*: indicate block will never be used again

## Flash-optimized file systems
* Lots of academic research projects, but none in widespread desktop use
* F2FS - used in Android phones since mid 2010s
* Need raw flash access, but no standard for that
* One approach: log-structured file system (entire fs is a log)

# Nonvolatile memory
Example: Intel 3D XPoint (Optane)
* Capacity: 512GB per DIMM
* Read: 300ns
* Write: 100ns
* Comparable to DRAM (100ns read/write)
* For FS, faster than flash
* Can't harness raw performance due to software overhead
    - Need new data model
    - Current use: caching for larger HDDs to approximate flash speeds

Open question: This is nearly as fast as DRAM. If this becomes as fast as DRAM, can we make *all* memory non-volatile? And what paradigm shifts could that cause?