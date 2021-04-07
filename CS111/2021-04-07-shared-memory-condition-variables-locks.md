# Shared memory
## Which variables are shared?
* Programmer decides
* All memory potentially shareable (in process)
* Stack locals: private
* Globals: shared
* Pointers stored in globals: shared
* Pointers passed as arguments when instantiating threads: shared

```C
void my_func(int x) {
    int y; // private
    Pool p; // private
}

Pool p2; // public
```

## Thread safety
* Refers to a class designed for shared use
* `std::vector` is not thread-safe
* For thread-unsafe classes: must manually lock externally

# Condition variables

`std::condition_variable`

Wait for particular state:

`wait(lock)`: atomically releases lock, blocks thread (reacquire lock before returning). On return condition *may* not exist

`notify_one()`: if any waiting threads, wake up the first one

`notify_all()`: wake all waiting threads

# Using locks
## How many locks?
* As few as possible
* One lock for program doesn't allow for concurrency
* Kernel-call lock causes contention
* Lock per variable is complicated, deadlock-prone, and inefficient

## Monitor:
* Shared data structure (class instance)
* One lock
* More than 1 condition variable
* Every method of class: 
    - Lock at beginning
    - Unlock at end