# File systems
Issues:
* Disk space management
* File naming/lookup
* Reliability, file recovery
* Protection: isolating and sharing user data

## File
Definition: Named collection of bytes stored durably

Access patterns:
* Sequential
* Random access: access by position

Notes:
* Most files are small: need to keep file metadata very small
* Large files occupy most of the disk
* Most IO for large files
* Files can grow

## Inode
Definition: OS info about a file
* Stored on disk
* Kept in memory (mostly while file open)

Contents:
* Size
* Sectors
* Access times
* Protection

## File allocation schemes

### Contiguous allocation (extent-based)
* File is a contiguous range of sectors
* Inode stores just first sector and last sector
* Free list: unused areas of disk

Advantages:
* Simple
* Fast sequential and random access
* Few seeks

Disadvantages:
* Fragmentation
* Must pre-declare file size
* Can't extend files in place

### Linked files
* Pick block size (ex. 4kb)
* Maintain linked list of free blocks
* File = linked list (each block points to next)
* Inode stores first block

Advantages:
* Block size matches with page size
* Easy to grow
* No external fragmentation
* Can share blocks between files

Disadvantages:
* Random access is very slow
* Sequential access requires seek per every block