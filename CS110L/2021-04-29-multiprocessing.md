# C: `fork()`
Why it's problematic:
* Accidentally nesting forks when spawning multiple children
* Children can execute code they weren't supposed to
* Accessing data structures during threading
* Zombie children if `waitpid()` isn't called

# C: `execvp()`
* Execute code we want to run concurrently in a separate executable, using arguments or pipes for args
* Simple and powerful: can make any changes to environment before executing a program, but this isn't *easy*

## Common multiprocessing paradigm to replace `fork` and `execvp`
* `fork()` and `exec()` still exist
* Define higher-level abstraction for common cases
    - ex. `subprocess` in Python

## Rust: `command`

### Building a `command`
```rust
Command::new("ps").args(&["--pid", &pid.to_string(), "-o", "pid= ppid= command="])
```

### Spawning processes
#### No concurrency: get output in buffer
```rust
let output = Command::new("ps")
                .args(&["--pid", &pid.to_string(), "-o", "pid= ppid= command="])
                .output()
                .expect("Failed to execute subprocess);
```

#### No concurrency: get status code, don't swallow output
```rust
let status = Command::new("ps")
                .args(&["--pid", &pid.to_string(), "-o", "pid= ppid= command="])
                .status()
                .expect("Failed to execute subprocess);
```

#### With concurrency: spawn and immediately return
```rust
let child = Command::new("ps")
                .args(&["--pid", &pid.to_string(), "-o", "pid= ppid= command="])
                .spawn()
                .expect("Failed to execute subprocess);
let status = child.wait();
```

#### Pre-exec function:
```rust
use std::os::unix::process::CommandExt;

fn main() {
    let cmd = Command::new("ls");
    unsafe {
        cmd.pre_exec(some_fn);
    }
    let child = cmd.spawn();
}
```
* `unsafe` block disables compiler checking with regards to memory allocation or shared structure accesses with multithreading

# Pipes
Issues:
* Leaked file descriptors
* Calling `close()` on bad values

## Rust pipe type

### Writing to an stdin pipe
```rust
let mut child = Command::new("cat)
        .stdin(Stdio::piped())
        .stdout(Stdio::piped())
        .spawn()?;

child.stdin.as_mut().unwrap().write_all(b"Hello, world!\n");
let output = child.wait_with_output()?;
```
Note: can also create arbitrary pipes with `os_pipe` crate
