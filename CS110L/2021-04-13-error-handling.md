# Error handling

## Error handling in C
* If a function can encounter an error, its return type is `int` or `void *`
    - If successful, it returns 0, otherwise, returns -1 (or valid pointer/`NULL`)
    - Function that encountered error sets global variable `errno` to be an integer indicating what went wrong (many such codes). If -1 or `NULL` was returned, caller should check `errno`.
* Heavy burden on programmer to remember how each specific function works
* Handling specific errors using `errno` creates mess of `if` statements

## Error handling using exceptions (most languages including C++)
* Error happens: error propagates up the stack until handled by `try/catch` or reaches `main` and crashes program
* Errors will not go unnoticed, unlike C: avoids undefined behavior
* Disadvantages:
    - Overhead in terms of code size
    - Failure modes become hard to reason about; any function can throw any exception at any time

## Error handling in Rust

### Enum (enumeration)
* Type that can contain one of several variants

```rust
enum TrafficLightColor {
    Red,
    Yellow,
    Green,
}

let current_state : TrafficLightColor = TrafficLightColor::Green;
```

Match expression: like a `switch` statement, but all possible variants must be covered.

```rust
fn drive(light_state : TrafficLightColor) {
    match light_state {
        TrafficLightColor::Green => go();
        TrafficLightColor::Yellow => slow_down();
        TrafficLightColor::Red => stop();
    }

    match light_state {
        TrafficLightColor::Green => go();
        _ => stop(); // default binding
    }
}
```

Rust `enum`s can store arbitrary data:

```rust
enum Location {
    Coordinates(f32, f32),
    Address(String),
    Unknown,
}

fn print_location (loc : Location) {
    match loc {
        Location::Coordinates(lat, long) => {
            println!("({} {})", lat, long);
        },
        Location::Address(addr) => {
            println!(addr);
        },
        Location::Unknown {
            println!("Location unknown!");
        }
    }
}

print_location(Location::Address("353 Jane Stanford Way".to_string()));
```

### Result
* Enum that represent successful returns and errors
    - Function ran successfully: return `Ok(return_value)`
    - Function errored: return `Err(error_object)`

Defined in Rust standard library as

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

Usage:

```rust
fn gen_num_sometimes() -> Result<u32, &'static str> {
    if get_random_num() > 10 {
        Ok(get_random_num());
    } else {
        Err("Spontaneous failure!");
    }
}

fn main() {
    match gen_num_sometimes() {
        Ok(num) => println!("Got number: {}", num),
        Err(message) => println!("Operation failed: {}", message),
    }

    let random_number = match gen_num_sometimes() {
        Ok(num) => num,
        Err(message) => println!("Operation failed: {}", message),
    }
}
```

Why this is useful: Problem in C was that it was easy to miss error; now it is obvious from function signature that an error can be returned.

However: error handling is still verbose!

#### The ? operator

Compact error handling: shorthand expansion of `Result`

```rust
fn helper() -> Result<T, E> {
    return do_something();
}

fn main() {
    // if helper returns Ok(value), set val = value
    // if helper returns Err(some_err), stop and return/propogate that error
    let val : T = helper()?;
}
```

```rust
fn read_file(filename : &str) -> Result<String, io::Error> {
    let mut s = String::new();
    File::open(filename)?.read_to_string(&mut s)?;

    // return
    Ok(s)
}
```

### Panics
* Errors we don't wish to propogate/handle
    - Serious/unrecoverable errors
    - Error that we don't anticipate happening/don't want to put effort in handling

`panic!` macro crashes program immediately with error message:

```rust
if serious_issue() {
    panic!("Sad times!");
}
```

* Library functions may return a `Result` with an `Err` that we don't want to handle gracefully
    - `Result::unwrap()` and `Result::expect()` let us take the `Ok` value, panicking if we got an `Err`.
    - `Result::expect()` will add a more descriptive message while panicking

```rust
let mut input = String::new();
io::stdin().read_to_string(&mut input).expect("Failed to read from stdin");
```

### Option
Like `Result`, for handling `None` (`NULL`).

```rust
fn feeling_lucky() -> Option<String> {
    if get_random_num() > 10{
        Some(String::from("I'm feeling lucky!"))
    } else {
        None
    }
}

fn main() {
    match feeling_lucky() {
        Some(num) => println!("{}", num),
        None => println!("Not feeling lucky :/");
    }

    // check if None or Some
    if feeling_lucky().is_none() {
        println!("Not feeling lucky :(");
    }

    if feeling_lucky().is_some() {
        println!("Feeling lucky :)");
    }

    // unwrap(), expect() work
    let message = feeling_lucky.unwrap();
    let message = feeling_lucky.expect("Function failed");

    // add a default value if None was returned
    let message = feeling_lucky.unwrap_or("Default value if None was returned");

    // ? operator also works with Option
    let expanded_message = feeling_lucky()? + " Are you?";
}
```