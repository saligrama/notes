# Why Rust (from a memory perspective)

What makes good code?
* Pre/post conditions to break code into small pieces with well-defined interfaces in between
    - Want to reason about small pieces in isolation
    - If pre/post conditions of each piece is held up in isolation, then simply stringing them together works without having to keep entirety of program in one's head
* Code with good memory management clearly defines how memory is passed around and "who" is responsible for cleaning it up

C/C++'s compiler cannot effectively verify pre/post conditions with regards to memory. Rust's does.

# Memory ownership

## Rules
* Each value in Rust has a variable that is its owner
* There can be only one owner at a time
* When the owner goes out of scope, the value gets dropped

These rules are enforced at compile time!

## Basic example

```rs
fn main() {
    let a = Bear::get();
}
```

`a` is the owner of the string and can do anything with it (ex. use member functions), and is responsible for freeing the memory.

```rs
fn main() {
    let a = Bear::get();
    let b = a;
}
```

Now, `b` is the owner and has the same access privileges and responsibility; whereas `a` relinquishes all privileges and responsibilities.

## Borrowing
```rs
fn main() {
    let a = Bear::get();
    my_cool_bear_function(&a);
    /* a still has ownership and still can be used */
}
```

`my_cool_bear_function` *borrows* the bear, but eventually gives it back to `a`, at which point `a` is responsible for freeing it.