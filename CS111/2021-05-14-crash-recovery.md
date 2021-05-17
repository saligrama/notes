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