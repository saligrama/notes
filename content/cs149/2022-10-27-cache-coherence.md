# Cache coherence

## Brief background on caches

* Recall: die size of CPU is significantly occupied by cache!
    - L3: per chip, one bank per core
    - L2, L1: private per core
    - Lower levels: faster/closer access for a core
* Associativity: flexibility of where a cache can put a memory address
* 4C's cache miss model:
    - Cold: first access, never seen before
    - Capacity: cache is finite size, data evicted
    - Conflict: when cache is not fully associative
    - **Coherence**

### Review: cache design

* Example: suppose code executes `int x = 1;` where `x` corresponds to address `0x12345604` in memory
* Cache line: data is 64 bytes on modern Intel processors
    - e.g. suppose 32-bit memory address, then need:
        - Line offset: 6 bits
        - Tag: 26 bits
        - Dirty bit: has the data been modified in memory?
* Behavior of write-allocate, write-back cache on write miss (uniprocessor case):
    1. Processor performs write to address that misses in cache
    2. Cache selects location to place line in cache
        - If dirty line in location, line written out to memory
    3. Cache loads line from memory (allocates cache line)
    4. Cache line fetched and 32 bits updated
    5. Cache line marked as dirty

## The cache coherence problem

* Modern processors replicate mem contents in local caches
    - However: processors can observe different values for the same memory location - caused by data replication to cache
* Memory system should behave: when accessing value at address X, should return last value written to X by *any processor*
    - Easier on a uniprocessor system: CPU and DMA are only source of writes

### Definition of coherence

Memory system is coherent if:

1. Results of parallel execution are such that for each mem. location, there exists a hypothetical serial ordering of all program operations consistent with execution results
2. Memory operations issued by any processor occur in the order they were issued
3. Value returned by a read is the value written by last write to location, as given by the serial ordering

### Required invariants

For any memory address, and at any given time period (epoch):

1. Single-Writer, Multiple-Reader invariant
    - Like RW-lock
    - At read-write epoch, only one possible writer
    - At read-only epoch: unlimited readers
2. Data-value invariant: write serialization
    - Value of mem address at start of epoch is same as value at end of previous read-write epoch

### Implementing coherence

* Software-based solutions (coarse-grained: over virtual memory pages)
    - OS uses page-fault mechanism to propagate writes
    - Can be used to implement memory coherence over clusters
    - Performance problem: false sharing
* Hardware-based solutions (fine-grained: over cache lines)
    - "Snooping"-based coherence
    - Directory-based coherence

### Snooping cache-coherence schemes

* Main idea: all coherence-related activity broadcasted to all processors in the system (to cache controllers)
* Cache controllers monitor memory operations, and follow cache coherence protocol to maintain coherence
    - Upon write, cache controller broadcasts invalidation message
    - As a result, next read from other processors will trigger cache miss
    - Processor retrieves updated value from memory due to write-through policy
* Dirty state of cache line indicates exclusive ownership (RW epoch)
    - Modified: cache is only cache with valid copy of line (can safely be written to)
    - Owner: cache responsible for propagating info to other processors when they attempt to load it from memory (otherwise a load will result in stale data)

### MSI write-back invalidation protocol

* Key tasks: ensure processor obtains exclusive write access, locating most recent copy of cache line's data on cache miss
* Three cache line states:
    - Invalid (I): same meaning of invalid in uniprocessor cache
    - Shared (S): line valid in one or more caches, memory up to date
    - Modified (M): line valid in exactly one cache (dirty, exclusive)
* Two processor operations (triggered by local CPU)
    - `PrRd` (read)
    - `PrWr` (write)
* Three coherence-related bus transactions from remote caches
    - `BusRd`: obtain copy of line with no intent to modify
    - `BusRdX`: obtain copy of line with intent to modify
    - `BusWb`: write dirty line out to memory
* Invalidation protocol:
    - Read obtains block in "shared", even if only cached copy
    - Obtain exclusive ownership before writing
        - `BusRdX` causes others to invalidate
        - If `M` in other cache, will cause writeback
        - `BusRdX` even if hit in `S`: promote to `M`
* Satisfaction of cache coherence:
    - SWMR invariant: satisfied as only one cache can be in `M`-state, all others get invalidation message
    - Multiple caches can be in read-only `S`-state

### MESI invalidation protocol

* MSI inefficiency: requests two interconnect transactions for common case of reading address, then writing to it
* Solution: add additional state `E` ("exclusive clean")
    - Line has not been modified, but only this cache has a copy of the line
    - Decouples exclusivity from line ownership (line not dirty, so copy in memory is valid copy of data)
    - Upgrade from `E` to `M` does not require bus transaction

### Scalable cache coherence using directories

* Snooping schemes broadcast coherence messages to determine state of line in other caches: not scalable
* Alternative idea: avoid broadcast by storing info. about status of the line in one place: a "directory"
    - Directory entry for cache line contains info about state of cache line in all caches
    - Caches look up info from directory as necessary
    - Cache coherence maintained by point-to-point messages between caches on "need-to-know" basis
* Still need to maintain SWMR and write serialization invariants

### Implications of cache coherence on programmer

* Communication is everything - comm. time is key parallel overhead!
* Cache synchronization appears as increased memory access time for multiprocessor