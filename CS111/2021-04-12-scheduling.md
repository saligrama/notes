# Single-core scheduling

## Computational resources
* Preemptible: Can give to a thread and can take it away
    - Network interfaces
    - Scheduling: how long thread keeps resources, who's next
* Non-preemptible: Can't take away without permission
    - File space
    - Allocation: who gets what

Deciding which resources are preemptible or not is associated with the cost to preempt.

Which thread should run on which core for how long?

## Goals
* Minimize response time (to useful result - down to human response time of 50-100ms)
* Use resources efficiently
    - Keep resources busy if there is work to do for them
    - Minimize context switches

## First-come-first-serve (FCFS/FIFO)
* Keep a ready queue of runnable threads
* When a thread becomes runnable, add it to the back
* When a core runs out of work to do (locks or exits), run first thread on queue

Disadvantage: One thread can monopolize core

## Round-robin
* Each thread gets to run for a given time slice, then goes to the back of the queue
* In practice: Time slices are in the range of 5-10ms (4ms in Linux)

For time slices: 
* Too large: slow response, since threads can monopolize cores
* Too small: context switching overhead means that resources won't be used effectively

## Shortest remaining processing time (SRPT)
* Schedule by least remaining processing time left
    - Breaks ties with FCFS
* Good for resource utilization
    - Imagine scenario with a CPU-bound and I/O-bound thread
    - CPU-bound thread gets low priority, I/O-bound thread gets high priority
    - CPU-bound thread runs while I/O-bound thread is blocked, once I/O-bound thread is unblocked it can run until it gets blocked again

Gives highest priority to least needy thread!

Disadvantage: must predict the future

## Priority-based scheduling

* Idea: Every thread has a priority maintained as part of its state
* Run the highest priority thread
    - Use round-robin to tiebreak
* Dynamically adjust priorities to approximate SRPT

### Priority queues
* Maintain many ready queues
    - Threads using least resources stay in highest priority queue
    - Threads using most resources stay in lowest priority queue
* Dispatcher runs threads from highest priority occupied queue

### 4.4 BSD Unix scheduler
* Track threads' recent CPU usage
* Give highest priority to thread with least usage
* No starvation: if a thread isn't getting executed, its priority will eventually rise and be run
* Many runnable threads: round-robin
* Linux biases scheduling with "nice" value: higher value means that thread yields CPU cycles, lower value means that thread tries to get more CPU cycles