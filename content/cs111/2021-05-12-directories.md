# Inode allocation
* Where on disk for inodes?
* Contiguous array at outer edge: lots of seeks
* Many arrays, spaced over disk
    - FS tries to allocate data for an inode near the inode itself
* Index in array: i-number, unique identifier for a file

# Directories
* Map from name to i-number
* Previously: one directory per disk, then per user
* Today: hierarchical directories
    - Stored like regular files
    - inode has special bit indicating it's a directory
* Root directory has i-number 2

## Procedure for looking up file
File: `/a/b/c`
1. root inode
2. `/` directory file (`a`)
3. inode for `/a`
4. first block of `/a` (find `b`)
5. inode for `/a/b`
6. first block of `/a/b` (find `c`)
7. inode for `/a/b/c`
8. first block of `/a/b/c`

## Working directories
* OS stores i-number in process control block
* This represents current working directory
* Lookup: leading slash: inumber 2, else cwd

## Links

### Hard links
* Multiple directory entries for an inode
* Reference count in inode
* No circularities because can't hard link to directories

### Symbolic (soft) links
* Symbolic link: special file
* Contents: path name
* During lookup: symbolic link? prepend link to path

`ln -s /e/f /a/b`

* Better for directories