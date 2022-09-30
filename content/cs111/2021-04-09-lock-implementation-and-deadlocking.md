# Implementing locks

For multiple cores:
* Atomic operations: read-modify-write
* Swap:
    - Reads word
    - Writes new value
    - Returns *old* value

## Version 1: Spin Lock
```C++
class Lock {
    Lock() {}
    std::atomic<int> locked(0);
}

void Lock::lock() {
    while (locked.swap(1)) {
        // do nothing
        // continue looping until we get a 0 back (old value)
    }
}

void Lock::unlock() {
    locked = 0;
}
```

## Version 2: less busy waiting
```C++
class Lock {
    Lock() {}
    std::atomic<int> locked(0);
    ThreadQueue q;
}

void Lock::lock() {
    if (locked.swap(1)) {
        q.add(currentThread);
        blockThread();
    }
}

void Lock::unlock() {
    if (q.empty()) {
        locked = 0;
    } else {
        unblockThread(q.remove());
    }
}
```

# Deadlocking

**Definition**: Several blocked threads such that each thread waits for a resource owned by another thread. No thread can make any progress.

## Example

Thread A:
```C++
lock_acquire(l1);
lock_acquire(l2);
...
lock_release(l2);
lock_release(l1);
```

Thread B:
```C++
lock_acquire(l2);
lock_acquire(l1);
...
lock_release(l1);
lock_release(l2);
```

This gets stuck, because each thread needs a resource from the other to proceed.

## Conditions for deadlocking
* Limited access
* No preemption
* Threads must make multiple independent requests
* Circularity of requests

## Complications
* Deadlocks can occur over any resource that blocks
    - Locks
    - Network requests
    - File I/O
* Deadlocks can occur over both discrete and continuous resources
* Can't predict what a thread will need

## Breaking deadlocks
After a deadlock is detected, break it by terminating one thread. This is not practical for operating systems, but useful for database systems

## Prevention
Need to prevent all 4 conditions for deadlocking from occurring at the same time.

* ~~No exclusive access?~~
    - Impossible because definition of a mutex
* ~~Preemption~~
    - Could cause program to misbehave
* Ask for all resources at once
    - Tricky and inconvenient; difficult to implement
    - Could overallocate, but this would be difficult and inefficient
* Prevent circularities
    - Must establish an order of allocating resources