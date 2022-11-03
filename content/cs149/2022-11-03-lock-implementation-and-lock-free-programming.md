# Locks, Fine-Grained Synchronization, and Lock-Free Programming

## Terminology

* Deadlock
    1. Mutual exclusion: only one processor can hold a given resource at a time
    2. Hold and wait: processor must *hold* resource while *waiting* for other resources it needs to complete an operation
    3. No preemption: processors don't give up resources until operation they wish to perform is complete
    4. Circular wait: waiting processors have mutual dependencies (e.g., cycle exists in dependency resource graph)
* Livelock: system is execution many operations, but no thread is making meaningful progress (e.g. continuous abort and retry)
* Starvation: system is making overall progress, but some processes make no progress
    - Usually not a permanent state

## More on atomics

### Implementing atomic operations from compare-and-swap

```c
// atomicCAS: "compare-and-swap"
// performs following logic atomically
int atomicCAS(int *addr, int compare, int val) {
    int old = *addr;
    *addr = (old == compare) ? val : old;
    return old;
}

void atomicMIN(int *addr, int x) {
    int old = *addr;
    int new = min(old, x);
    while (atomicCAS(addr, old, new) != old) {
        old = *addr;
        new = min(old, x);
    }
}
```

## Lock implementation

### Test-and-set based lock

Atomic test-and-set instruction: `ts`
* In reality: `cmpxchg` in x86, e.g. `lock cmpxchg dst, src`

Spin lock implementation:

```arm
ts r0, mem[addr] // load mem[addr] into r0
                 // if mem[addr] is 0, set mem[addr] to 1

lock:
    ts r0, mem[addr] // load word into r0
    bnz r0, lock // if 0, lock obtained, otherwise go back to fn start

unlock:
    st mem[addr], #0 // store 0 into mem addr
```

### Test-and-test-and-set lock

```c++
void Lock(int *lock) {
    while (true) {
        // while another processor has the lock, spin
        // (assume *lock is not register allocated)
        while (*lock != 0);

        // when lock is released, try to acquire it
        if (test_and_set(*lock) == 0)
            return;
    }
}

void Unlock(int *lock) {
    *lock = 0;
}
```
* Performance is better because instead of test-and-set per spin, we keep trying to read the lock, and do only one test-and-set

### Ticket lock

* Main problem with test-and-set style locks: upon release, all waiting processors attempt to acquire lock using test-and-set
* Instead, ticket system:

```c
struct lock {
    int next_ticket;
    int now_serving;
}

void Lock(lock *l) {
    int my_ticket = atomic_increment(&l->next_ticket);
    while (my_ticket != l->now_serving);
}

void Unlock(lock *l) {
    l->now_serving++;
}
```

## Using locks

Motivating example: sorted linked list

```c++
struct Node {
    int value;
    Node *next;
}

struct List {
    Node *head;
}

void insert(List *list, int value) {
    Node *n = new Node;
    n->value = value;

    Node *prev = list->head;
    Node *cur = list->head->next;
    
    while (cur) {
        if (cur->value > value)
            break;
        
        prev = cur;
        cur = cur->next;
    }
    
    n->next = cur;
    prev->next = n;
}

void delete(List *list, int value) {
    Node *prev = list->head;
    Node *cur = list->head->next;

    while (cur) {
        if (cur->value == value) {
            prev->next = cur->next;
            delete cur;
            return;
        }

        prev = cur;
        cur = cur->next;
    }
}
```
* Issues when called with multiple threads
    - Simultaneous insertion: both threads may computer same `prev` and `cur`, resulting in one insertion getting lost
    - Simultaenous insertion/deletion: same issue may cause entire loss of part of list

### Attempt 1: global data structure lock

```c++
struct Node {
    int value;
    Node *next;
}

struct List {
    Node *head;
    Lock *lock;
}

void insert(List *list, int value) {
    lock(list->lock);
    Node *n = new Node;
    n->value = value;

    Node *prev = list->head;
    Node *cur = list->head->next;
    
    while (cur) {
        if (cur->value > value)
            break;
        
        prev = cur;
        cur = cur->next;
    }
    
    n->next = cur;
    prev->next = n;
    unlock(List->lock);
}

void delete(List *list, int value) {
    lock(list->lock);
    Node *prev = list->head;
    Node *cur = list->head->next;

    while (cur) {
        if (cur->value == value) {
            prev->next = cur->next;
            delete cur;
            unlock(list->lock);
            return;
        }

        prev = cur;
        cur = cur->next;
    }

    unlock(list->lock);
}
```
* But this is slow! serializes lock usage

## Second attempt: hand-over-hand locking

Lock per node.

![Fine-grained locking](https://gfxcourses.stanford.edu/cs149/fall22content/media/lockfree/images/slide_045.jpg)