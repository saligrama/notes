# Transactional memory

## The limitations of locking

* Locks force tradeoffs between:
    - Degree of concurrency (performance)
    - Chance of races, deadlock (correctness)
* Coarse grained locking: lowers concurrency, higher chance of correctness
    - e.g. single lock for the data structure
* Fine-grained locking: high concurrency, lower chance of correctness
    - e.g. hand-over-hand locking
* Better synchronization abstractions?

### Review: ensuring atomicity via locks

```c
void deposit(Acct account, int amount) {
    lock(account.lock);
    int tmp = bank.get(account);
    tmp += amount;
    bank.put(account, tmp);
    unlock(account.lock);
}
```
* Deposit: Read-modify-right operation; want deposit to be atomic with respect to other bank operations on the account
    - Locks help ensure this atomicity by ensuring mutual exclusion on the account

## Programming with transactions

* What if we could declare a section of code atomic?

```c++
void deposit(Acct account, int amount) {
    atomic {
        int tmp = bank.get(account);
        tmp += amount;
        bank.put(account, tmp);
    }
}
```
* Atomic construct is declarative: programmer states *what to do* (maintain code atomicity), not *how to do it*
    - System could implement this in multiple ways - using locks, for example
    - Optimistic concurrency: maintain serialization only in situations of true contention (R-W or W-W conflicts)

### Declarative vs. imperative abstractions

* Declarative: programmer defines what should be done (e.g. execute 1000 independent tasks)
* Imperative: programmer states *how* it should be done (e.g. spawn N worker threads, assign work to threads via removing work from shared task queue)

## Transactional memory (TM) semantics

* Memory transaction
    - Atomic and isolated sequence of mem. accesses
    - Inspired by database transaction
* Atomicity: all or nothing
    - Upon transaction commit, all memory writes in transaction take effect as one
    - On transaction aborts, none of the writes appear to take effect, as if transaction never happened
* Isolation
    - No other processor can observe writes before transaction commits
* Serializability
    - Transactions appear to commit in single serial order
    - However: no exact order of commits guaranteed by semantics of transaction

## Motivating example: Java HashMap

Map: key -> value, implemented as hash table with linked list per bucket

### Sequential implementation

```java
public Object get(Object key) {
    int idx = hash(key);
    HashEntry e = buckets[idx];
    while (e != null) {
        if (equals(key, e.key)) {
            return e.value;
        }
        e = e.next;
    }
    return null;
}
```
* Bad: not thread safe (when sync. needed)
* Good: no lock overhead when sync. not needed

### Java 1.4 solution: synchronized layer

```java
public Object get(Object key) {
    synchronized (myHashMap) {
        return myHashMap.get(key);
    }
}
```
* Convert any map to thread-safe variant
* Use explicit, coarse-grained mutual locking specified by programmer
* Good: thread-safe, easy implementation
* Bad: limits concurrency, poor stability
* **A better option**: finer-grained locking (e.g. lock per bucket)
    - Thread-safe, but incurs lock overhead even if sync. not needed

### Transactional HashMap

* Simply enclose all operations in atomic block
    ```java
    public Object get(Object key) {
        atomic {
            return m.get(key);
        }
    }
    ```
* Good: thread-safe, easy to program
* Performance and scalability: depends on workload and implementation of atomic (to be discussed)

## Another motivation: failure atomicity

Initially:

```c++
void transfer (A, B, amount) {
    synchronized(bank) {
        try {
            withdraw(A, amount);
            deposit(B, amount);
        } catch (exception1) {
            rollback(code1);
        } catch (exception2) {
            rollback(code2);
        }
    }
}
```
* Complexity of manually catching exceptions:
    - Programmer has to specify edge cases, can miss some
    - Undo code may be tricky to implement partially

With transactions:

```c++
void transfer (A, B, amount) {
    atomic(bank) {
        withdraw(A, amount);
        deposit(B, amount);
    }
}
```
* System now responsible for processing exceptions
    - All exceptions (except those managed explicitly by programmer)
    - Transaction aborted and memory updates are undone

## Another motivation: composability

```c++
void transfer(A, B, amount) {
    synchronized(A) {
        synchronized(B) {
            withdraw(A, amount);
            withdraw(B, amount);
        }
    }
}
```
* Deadlocks when A, B try to transfer to each other
* Composing lock-based code can be tricky: requires system-wide policy to get correct, but these can break software modularity

With transactions:

```c++
void transfer(A, B, amount) {
    atomic(bank) {
        withdraw(A, amount);
        deposit(B, amount);
    }
}
```
* Transactions theoretically compose gracefully
    - Programmer declares global intent: atomic execution of transfer; no need to know about global implementation strategy
* Transaction in `transfer` subsumes any defined in `withdraw` and `deposit`
    - System manages concurrency as well as possible: serialization when needed (contention), concurrenct otherwise

## The difference between atomicity and locks

* Atomic: high-level declaration of atomicity
    - Does not specify the implementation
* Lock: low-level blocking primitive
    - Does not provide atomicity or isolation on its own
    - Can be used to implement atomic block, but can be used for purposes beyond atomicity
        - Cannot replace all locks with atomic regions
    - Atomicity eliminates many data races, but programming with atomic blocks can still suffer from atomicity violations

## Implementing transactional memory

* TM systems must provide atomicity and isolation
    - All writes must take effect all at once
    - All or nothing: failure leads to no write taking effect
    - No reads observed before all writes commit

### Data versioning policy

* Manage uncommitted (new) and previously committed (old) versions of data for concurrent transactions
* Eager versioning: undo-log based
    - Update memory immediately, maintain "undo log" in case of abort
    - Good: faster commit (data already in memory)
    - Bad: slower abort, fault tolerance issues (consider crash in middle of transaction)
    - Need to check something else is not reading/writing in middle of the eager versioning update block
* Lazy versioning: write-buffer based
    - Log memory updates in transaction write buffer, flush buffer on failure
    - Update actual memory location on commit
    - Good: faster abort (just clear log), no fault tolerance issues
    - Bad: slower commits

### Conflict detection policy

* Must detect and handle conflicts between transactions
    - Read-write conflict: transaction A reads address X, which was written to by pending (but not yet committed) transaction B
    - Write-write conflict: transactions A and B are both pending, and both write to address X
* System must track a transaction's read set and write set
    - Read-set: addresses read during the transaction
    - Write-set: addresses written during the transaction
* Pessimistic detection: check for conflicts immediately during loads and stores
    - Contention manager decides to stall or abort transaction on detection of conflict
    - Good: detect conflicts early -> faster and less work on abort
    - Bad: no forward progress guarantees, more aborts in some cases
    - Bad: fine-grained communication: check on each load/store
    - Bad: detection on critical path
* Optimistic detection: detects conflicts when transaction attempts to commit
    - On conflict, give priority to committing transaction; other transactions may abort later on
    - Good: forward progress guarantees
    - Good: bulk communication and conflict detection
    - Bad: detects conflicts late, can still have fairness problems