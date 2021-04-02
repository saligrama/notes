# Concurrency

## Independent threads

Definition: a thread that can't effect or be affected by any other thread (execution state isn't shared with other threads)

Properties:
* Deterministic: input state exactly determines results
* Reproducible
* Scheduling order irrelevant

Example: arithmetic operations

In practice: this almost never happens; thread states are almost always shared within the OS

## Cooperating threads
Definition: Cooperating threads have a shared state

Properties:
* Nondeterministic
* Not necessarily reproducible

Example: suppose these are running on different threads

```C
printf("ABC")
```

```C
printf("CBA")
```

Possible outputs:

```
ABCCBA
CBAABC
ACBBCA
ABCBCA
ABCBAC
etc...
```

Note with `ABCCBA` we can't tell which thread the first `C` came from, because we don't know the interleaving pattern!

### Why cooperation?
* Share resources
    - Disk
* Performance
    - Read one block of a file while processing previous block
    - Subdivide jobs

Not all ordering matters: doesn't matter if instructions access entirely independent variables/data

But order matters if, for example:

```C
A = B + 1
```

```C
B = B * 2
```

are running on separate threads. This is called a **race**. Result depends on which thread finishes last.

## Atomic operations
Definition: Operation that occurs in its entirety without being interrupted.

Properties:
* Can't observe internal states
* Single-word (of memory) references and assignments
* Can't produce atomic operations out of nonatomic operations
* Can use one atomic operation to create others

Example: suppose `printf()` was an atomic operation. With the following on separate threads:

```C
printf("ABC")
```

```C
printf("CBA")
```

Possible outputs:

```
ABCCBA
CBAABC
```
