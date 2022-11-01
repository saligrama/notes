# Memory consistency

* Definition of consistency: allowed behavior of loads and stores to different addresses in parallel system

## Memory operation ordering

* Program defines sequence of loads and stores
* Four types of orderings:
    - Wx -> Ry: write to X must commit before subsequent read from Y
    - Rx -> Ry: read from X must commit before subsequent read from Y
    - Rx -> Wy: read from X must commit before subsequent write to Y
    - Wx -> Wy: write to X must commit before subsequent write to Y

## Sequential Consistency

* All ops executed in some sequential orders
    - As if manipulation if single shared memory
* Each thread's operations happen in program order
    - Maintains all four memory operation orderings

## Relaxed consistency

* Allow certain orderings to be violated
* Allows us to hide memory latency: overlap memory access operations with other operations when they are independent
    - Mem. access in cache coherent system may require more work than just reading bits from memory