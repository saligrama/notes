# Virtual memory schemes

## Segmentation
Process has multiple segments of memory (code, data, stack)

MMU has a segment map with one entry per segment, with three fields:
* Base
* Bound
* Writeable bit (is process allowed to modify the segment)

Segment number can be from:
* Top bits of virtual address
* Implicit from instruction
    - Instructions vs data
    - x86 prefixes

Advantages:
* Manage segments separately
* Move/swap to disk
* Share code segments between processes
    - Have same base/bound numbers

Disadvantages:
* Fragmentation (variable length)
* Can't have too many segments
* Rigidly divides UAs

## Paging
A *page* is a fixed size unit of virtual/physical memory (i.e., 4kb)

Page table fields:
* Base (physical page number)
* Writeable bit
* Present

Virtual address (page number and offset) used to index into the page table

Advantages:
* No fragmentation
* List of free pages
* Page size == disk block size

Disadvantages:
* Page maps get *LARGE*
    - In x86-64, full page tables can go up to 512GB