# Crash recovery
* Crashes can happen *anywhere*
* Lost data
* Inconsistency
    - Modification affects several blocks
    - Add block to file: modify 2 blocks (bitmap, inode)
* "Atomic ops": all or nothing for multi-block ops?

## fsck
* Fix on reboot
* Checks to see if system shutdown cleanly
    - Set flag on disk during normal shutdown
    - If hard shutdown, flag won't be set - then run fsck
    - Reboot: clear flag
* If not, scan disk, find and fix inconsistencies

Goals:
* Restore consistency
* Minimize information loss

Issues:
* Can still lose information
* System can be unusable
* Security issues: sensitive information can be duplicated to a different file
* Slow
    - Read entire 5TB disk sequentially: 8hr, 10% randomly: weeks

Possible scenarios:
1. Block exists in both file and free list
    - Solution: remove from free list
2. Inode reference count != number of directory entries
    - Solution: change reference count
3. Block in 2 different files
    - Solution: give it to newest file
    - Alternatively: duplicate block, give each a copy
4. Inode reference count nonzero, but no directory entries
    - Solution: Create link that is a directory entry in `/lost+found`

## Ordered writes
* Adding block to file:
    - Write free list ("allocated")
    - Write inode

Operation:
1. Initialize target before storing pointer
2. Nullify existing pointers before reusing target
3. Set new pointer to a live resource/target before clearing last pointer

Advantages:
* Can have block in both places

Disadvantages:
* Can lose block
* Performance: defeats cache for writing
* Resource leaks
    - Run fsck in background to recover lost resources

Optimization:
* No synchronous writes
* For each block in cache: other blocks to write first
* Write dependencies first

## Write-ahead logging (journaling file system)
Currently used in Linux ext3/ext4, Windows NTFS, Apple HFS+/APFS

Log: Detect/fix inconsistencies without full scan
* Before any operation: record info in the log
* Add block to file
* Sync update log
* After crash: replay log
    - At this point the system will be back to full integrity

Advantages:
* Recovery fast
* Eliminates inconsistencies
* Log written sequentially, and is fast
* Can delay metadata writes

Disadvantages:
* Log grows!
* Synchronous disk writes can lose data

Operation types:
1. Before flushing cache, write log to disk
2. For each cache block, last log position related to block

When flushing disk block, make sure log is flushed

### Log truncation
* Stop system, flush cache, truncate log
* Save head position, flush cache, keep operating
* Delete log entries older than saved head

### Log contents
* File metadata
* High level operations (i.e., add block *x* to inode *y* at index *z*)
* Physical operations (set bytes *x-y* of block *z* to *D*)
* Must group records

## fsync
* Kernel call that forces all data for a file to disk
* Can be invoked by program