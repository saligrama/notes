# Performance Optimization: Locality, Communication, Contention

## Message passing review

* Machine model: cluster of two machines, each with own processor/memory/cache, and communication via network (costly)
    - Note: communication can also be between cores on a chip, core and its cache, core and memory
* This model allows us to think of a parallel system as extended memory hierarchy, in terms of increasing latency:
    1. Processor
    2. Registers
    3. Local L1 cache
    4. Local L2 cache
    5. L2 from another core
    6. L3 cache
    7. Local memory
    8. Remote memory (one network hop)
    9. Remote memory (N network hops)
* Communication: via send/receive messages
    - *send*: specifies recipient, buffer to be transmitted, identifier (tag)
    - *receive*: sender, specifies buffer to store data, identifier (tag)
    - Only way for thread communication

### Synchronous (blocking) send/receive

* `send()`: call returns once sender receives acknowledgement that message data resides in address space of receiver
* `recv()`: call returns when data from received message copied into receiver address space and acknowledgement sent back to sender

### Non-blocking asynchronous send/receive

* `send()`: call returns immediately
    - Buffer provided to `send` cannot be modified by calling thread since message processing occurs concurrently with thread execution
    - Calling thread can perform other work while waiting for send
* `recv()`: posts intent to receive in the future, returns immediately
    - Use `checksend()`, `checkrecv()` to determine actual status of send/receipt
    - Calling thread can perform other work while waiting for receive

### Communicaton-to-computation ratio

* Fraction: (# bytes communication)/(# instructions computation)
* If denominator is execution time of computation, then ratio gives avg. bandwidth requirement of code
* Arithmetic intensity: 1/comm-comp ratio (higher is better)
    - High arithmetic intensity required to efficiently utilize modern parallel processors due to high ratio of compute capability to avail. bandwidth

## Inherent vs. artifactual communication

* Inherent communication: communication that *must* occur in a parallel program (i.e., essential to algorithm)
    - Good assignment decisions can reduce inherent communication
* Artifactual communication: all other communication (i.e., results from practical details of system implementation)
    - e.g. min. data transfer granularity greater than what must be transferred
    - e.g. cache size too small requiring reads from memory every time on an access

### Techniques for reducing communication

* Improving temporal locality by changing traversal order
    - e.g. "fusing" loops
* Exploit sharing: co-locate tasks that operate on same data
    - Schedule threads working on same data structure at same time on same processor

## Contention

* A resource can perform ops. at a given throughput (number of transactions per unit time)
    - e.g. memory, communication links, servers, network, etc.
* Contention occurs when many requests to a resource are made within a small window of time (makes resource a "hot spot")
* Can reduce by replicating contended resource (e.g. local copies, fine-grained locks) or staggering access to contended resources