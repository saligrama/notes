# Drop trait
* `drop` function called at end of scope (designated by curly braces)
* Special function that properly `free`s an entire object (maybe multiple pointers to free)
    - Similar to C++ destructor
* Classes that implement a `drop` function have a `Drop` trait

```rs
fn main() {
    let a = "Hello, world!".to_string();
    let b = a;
} // a, b dropped here: drop only called on b
```

# Copy trait
* Some values in Rust don't use the heap and are stored directly on the stack (integer types, booleans, etc)
* These are copied by default when assigning variables, so `drop` doesn't need to be called
* Types with this property have the `Copy` trait
* If a type has the `Copy` trait, it cannot also have the `Drop` trait

# Variable rules
* All pieces of data in Rust are by default immutable (i.e., every variable is default `const`)
* `mut` keyword allows a variable to be mutable (i.e., a reverse `const`)

Example:

```rs
let lst = vec![1,2,3];
vec.push(4);
```

is not allowed, but

```rs
let mut lst = vec![1,2,3];
vec.push(4);
```

is allowed.

# Borrowing type
* `&` creates a variable type that is a reference to the underlying type
* By default types created with `&` are immutable, but can be made mutable with `mut`
* Mutable references can be made only if the underlying variable is also mutable
* A single value can have an infinite number of immutable references, but only one mutable reference

Code examples:

```rs
let a = Bear::get();
let b = &a;
my_cool_bear_function(b);
```

```rs
let mut a = Bear::get();
let b = &mut a;
```

```rs
fn append_to_vector(lst : &mut Vec<u32>) {
    lst.push(3);
}

fn main() {
    let mut lst = vec![1, 2, 3];
    append_to_vector(&mut lst);
}
```