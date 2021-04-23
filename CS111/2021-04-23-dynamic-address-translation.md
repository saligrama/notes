# Dynamic Address Translation
Goal: Make a process think it has its own memory space independent from all other processes
* Process's memory would start at 0

Hardware has
* CPU core
* Memory management unit
    - Converts virtual addresses (process memory space) to physical addresses (actual hardware memory address)
* Memory

## Base and Bound
* Virtual address space goes from 0 to Bound
* Physical address for same process goes from Base to Base+Bound

Properties:
* Private memory per process
* Memory isolation
* Base and bound saved in PCB, set on context switch
* No load-time relocation

Notes:
* OS-level tasks have the MMU disabled
* User processes can't see base or bound
    - Processor Status (PS) bit: kernel vs user

How does OS regain control? Special instructions (traps)
* Branch into OS code (address in special memory location)
    - Save instruction pointer, PS bit
* Reset mode to kernel
* Return from trap:
    - Jump back to user
    - Reset PS bit

Advantages:
* Cheap
* Efficient
* Swapping to disk possible

Disadvantages:
* Fragmentation
    - Growing memory for process difficult/expensive
* One contiguous region per process: sharing impossible
* Limited stack growth
* Can't have read-only code