# Performance Optimization: Work Distribution and Scheduling

## Programming for high performance

* Iterative process: refine choices for decomposition, assignment, orchestration
* Goals (that can be at odds with each other):
    - Balance workload onto available execution resources
    - Reduce communication
    - Reduce extra work overhead
* Note: always implement the simplest solution first, then profile to figure out where and how to do better for best impact

## Balancing the workload

### Static assignment

* Assignment of work to threads not dependent on runtime factors
    - Note: distribution not determined at compile-time; can be impacted by provided parameters
* Good properties of static assignment:
    - Simple, near-zero runtime overhead for assignment (e.g. indexing math)
* Applicability:
    - When cost (execution time) and amount of work is predictable, allowing programmer to work out good assignment in advance
    - e.g. if known that all work has the same cost, just divide up jobs equally
    - e.g. if distribution of each task is predictable but unequal, can still come up with an assignment that achieves good utilization

### Semi-static assignment

* Cost of work is predictable for near-term future
    - Idea: recent past is a good predictor of near future
* Application periodically profiles execution and readjusts assignment
    - Assignment is "static" between readjustments

### Dynamic assignment

* Program determines assignment dynamically at runtime to ensure well-distributed load
    - Use when execution time of tasks, or number of tasks, is unpredictable
* Model using a shared work queue: list of work to do
    - Worker threads pull data from shared work queue
    - Push new work to queue as created
    - e.g. ISPC thread model
* Note: as we increase granularity of task partitioning (achieving better workload balance), we also increase synchronization overhead
    - Can decrease some overhead by having a thread per queue, and threads steal from other queues when they run out of work to do

## Fork-join parallelism

* So far: looked at data-parallelism and thread-management parallelism
* Now consider divide-and-conquer algorithms
    - Initial job creates additional jobs, that create additional jobs recursively, and so on, until base case
    - Notion of forking: creating additional jobs, and joining: waiting for those jobs to complete
* Cilk Plus: C++ language extension now in GCC
    - `cilk_spawn foo(args);`: invoke `foo`, but unlike standard function call, but unlike standard function call, caller may continue executing asynchronously with execution of `foo`
    - `cilk_sync;` returns when all calls spawned by current function have completed
        - Every function that contains `cilk_spawn` has implicit `cilk_sync` at end of function (i.e., when function returns, all associated work is complete)
* Idea for writing program: expose independent work (potential parallelism) to the system using `cilk_spawn`

### Cilk Plus examples

```c++
// foo, bar may run in parallel
cilk_spawn foo();
bar();
cilk_sync;
```

```c++
// foo, bar may run in parallel, dependency graph is different, potentially higher runtime overhead
cilk_spawn foo();
cilk_spawn bar();
cilk_sync;
```

```c++
// foo, bar, baz, qux may run in parallel
cilk_spawn foo();
cilk_spawn bar();
cilk_spawn baz();
qux();
cilk_sync;
```

### Abstraction vs. implementation in Cilk Plus

* `cilk_spawn` abstraction doesn't specify how or when spawned calls are scheduled to execute
    - Only that they *may* be run concurrently with caller
* `cilk_sync` serves as constraint on scheduling
    - All spawned calls must complete before `cilk_sync` returns

### Parallel Quicksort in Cilk Plus

```c++
void qsort(int *begin, int *end) {
    // sort serially if program is sufficiently small
    if (begin >= end - PARALLEL_CUTOFF) {
        std::sort(begin, end);
    } else {
        int *mid = partition(begin, end);
        cilk_spawn qsort(begin, mid);
        qsort(mid+1, last);
    }
}
```

### Fork-join scheduling

* Simple scheduler:
    - Launch `pthread` for each `cilk_spawn` using `pthread_create`
    - Translate `cilk_sync` into appropriate `pthread_join` calls
    - However: lots of overhead from context switching and spawn due to more concurrently running threads than physical execution units
* Instead, pool of worker threads (actual implementation):
    - All threads created at application launch; as many as physical execution contexts
        ```c++
        while (work_exists()) {
            work = get_new_work();
            work.run();
        }
        ```
    - When Cilk reaches a spawn, thread places continuation (i.e., rest of what is not being `cilk_spawn`ed) in its own queue
        - Available to other idle threads to do the work: "child stealing" vs. "continuation stealing"
        - Work queue implemented as deque (double-ended queue)
            - Normal operation: local thread pushes/pops from tail
            - When work stealing: remote threads pop from head
        - Stealing policy: idle threads randomly choose thread to attempt to steal from (random victim)
            - Child-first work stealing scheduler anticipates divide-and-conquer parallelism