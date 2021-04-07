# Synchronization

**Definition**: Using atomic operations to ensure correct operation of cooperating threads (includes both safety and liveness properties)

**Critical section**: Piece of code where only one thread can execute at once

**Mutual Exclusion (mutex)**: Mechanisms for implementing critical sections

## Computerized milk purchase

```C
if (milk == 0) {
    if (note == 0) {
        note = 1
        buy_milk();
        note = 0;
    }
}
```

This doesn't work, because thread 1 can see a zero value for `note` while thread 0 is setting `note` to 1.

Second attempt:

Thread A
```C
noteA = 1;
if (noteB == 0) {
    if (milk == 0) {
        buy_milk();
    }
}
noteA = 0;
```
Thread B
```C
noteB = 1;
if (noteA == 1) {
    if (milk == 0) {
        buy_milk();
    }
}
noteB = 0;
```
Problems:
* Asymmetric code
* Busy waiting
* Difficult to extend to more than two threads

## Mechanisms for synchronization
* Mutual exclusion (locks and semaphores)
* Blocking (condition variables)

# Locks
**Definition**: An object that can only be owned by one thread at a time

Lock operation:
1. Wait until unlocked
2. Lock it (setting locked bit)

Unlock operation:
1. Mark lock as free
2. If threads are waiting on the lock, wake up one of those threads

In this class: `std::mutex`

## Milk with locks

```C
std::mutex m;

m.lock();
if (milk == 0) {
    buy_milk();
}
m.unlock();
```