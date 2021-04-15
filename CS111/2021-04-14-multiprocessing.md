# Multiprocessor scheduling

## Simple approach
* Shared ready queue, lock
* One dispatcher per core
* Timer interrupts per core
* Core takes highest-priority thread and runs it

When a thread becomes runnable:
* If the new thread is higher priority, preempt existing thread

## Contention for lock and queues
* Have a separate ready queue per core
* Balance load across cores (work stealing)

## Work conservation
* If there is a ready thread currently queued, and there's an idle core, thread will run on that core

## Core affinity
* Expensive to move a thread to a new core
* Try to keep the thread on the same core

## Gang scheduling
* Run threads of a process together (on different cores)
* Otherwise, what happens is:
    - One thread locks
    - Deschedule
    - Other threads may block on lock
* Keep thread loaded even if blocked?
    - May unblock soon?
    - Busy waiting might be better in some cases!

The Linux kernel does not implement gang scheduling.