# 10. Unsafe Rust for C++ Veterans

> In C++, everything is unsafe by default—you opt into safety with smart pointers and RAII. In Rust, everything is safe by default—you opt out with `unsafe` blocks. The boundary is explicit, and that changes everything.

---

## Why This Matters to a C++ Developer

You're used to having complete control. You can cast away `const`, reinterpret memory, dereference raw pointers, and call functions through function pointers. The compiler trusts you to know what you're doing.

This is necessary for:
- Interfacing with C libraries
- Implementing low-level data structures
- Optimizing performance-critical code
- Working with hardware or memory-mapped I/O

You handle this through:
- Discipline ("Be careful with raw pointers")
- Code review ("Did you check for null?")
- Sanitizers (AddressSanitizer, UndefinedBehaviorSanitizer)
- Testing (and hoping you hit the edge cases)

The problem is that unsafe code looks like safe code. A raw pointer dereference looks the same whether it's guaranteed safe or potentially undefined behavior. The compiler doesn't distinguish. You have to.

---

## The C++ Mental Model

In C++, **everything is unsafe by default**. Safety is opt-in.

**Raw pointers are everywhere.**  
You use them for non-owning references, for C interop, for performance. The compiler doesn't track whether they're valid.

```cpp
int* ptr = get_pointer();
*ptr = 42;  // Is ptr valid? Is it null? Unknown.
```

**Casts are unchecked.**  
You can cast between pointer types, cast away `const`, or reinterpret memory. The compiler assumes you know what you're doing.

```cpp
const int* p = &value;
int* q = const_cast<int*>(p);  // Removes const. Your responsibility.
*q = 42;  // Undefined behavior if value was actually const.
```

**Undefined behavior is silent.**  
If you violate the rules (null dereference, out-of-bounds access, data race), the compiler doesn't stop you. You get undefined behavior, which might work, might crash, or might corrupt memory.

```cpp
int arr[3] = {1, 2, 3};
arr[10] = 42;  // Out of bounds. Undefined behavior. Compiles.
```

**Safety is a discipline.**  
You use smart pointers, RAII, and const-correctness to make your code safer. But nothing enforces this. You can always drop down to raw pointers and manual management.

**The boundary is invisible.**  
There's no marker that says "this code is unsafe." You have to read the code and understand the invariants.

This model works because:
- You have complete control
- You can optimize without restrictions
- You can interface with any C library

---

## Where the Model Breaks Down

The problem is that **unsafe code is indistinguishable from safe code**.

**You can't audit for safety.**  
There's no way to find all the unsafe operations in a codebase. Raw pointer dereferences, casts, and manual memory management are scattered throughout. You can't isolate them.

**Refactoring breaks invariants.**  
You change a function to return a pointer instead of a reference. The code compiles. But now some callers have dangling pointers. The compiler doesn't notice.

**Unsafe code infects safe code.**  
A bug in unsafe code can corrupt memory used by safe code. A data race in one thread can break invariants in another. The boundary is porous.

**Sanitizers are incomplete.**  
AddressSanitizer catches many bugs, but not all. It doesn't catch data races (that's ThreadSanitizer). It doesn't catch logic errors. It only helps if you run the code path that triggers the bug.

**The cost is deferred.**  
You write the code, it compiles, and you find out later whether it was safe. By then, the bug is in production, and you're debugging a crash with no clear cause.

Rust inverts this. Everything is safe by default. Unsafe operations require an `unsafe` block. The compiler enforces memory safety, thread safety, and lifetime correctness everywhere except inside `unsafe` blocks.

This makes the boundary explicit:
- You can audit for `unsafe` blocks
- You can isolate unsafe code and review it carefully
- You can trust that safe code is actually safe

The trade-off is that you have to justify every `unsafe` block. You have to document the invariants. You have to prove (to yourself and reviewers) that the unsafe code upholds Rust's safety guarantees.

This is more restrictive than C++. But it's also more honest: when you see `unsafe`, you know to be careful. When you don't, you can trust the compiler.

---


## Rust's Model

Rust makes the boundary between safe and unsafe code explicit. Everything is safe by default. Unsafe operations require an `unsafe` block.

**Unsafe is a keyword.**  
Five operations require `unsafe`:

1. Dereferencing raw pointers
2. Calling unsafe functions
3. Accessing or modifying mutable static variables
4. Implementing unsafe traits
5. Accessing fields of unions

```rust
fn read_raw_pointer(ptr: *const i32) -> i32 {
    unsafe {
        *ptr  // Dereference requires unsafe
    }
}
```

The `unsafe` block says: "I'm taking responsibility for upholding Rust's safety guarantees here."

**Raw pointers for C interop.**  
When interfacing with C, you use raw pointers:

```rust
use std::ffi::CString;
use std::os::raw::c_char;

extern "C" {
    fn strlen(s: *const c_char) -> usize;
}

fn get_length(s: &str) -> usize {
    let c_str = CString::new(s).unwrap();
    unsafe {
        strlen(c_str.as_ptr())
    }
}
```

The `unsafe` block isolates the C interop. The rest of your code is safe.

**Unsafe functions.**  
Functions that have safety requirements are marked `unsafe`:

```rust
unsafe fn write_to_address(addr: usize, value: i32) {
    let ptr = addr as *mut i32;
    *ptr = value;
}

fn main() {
    let mut x = 42;
    let addr = &mut x as *mut i32 as usize;
    
    unsafe {
        write_to_address(addr, 100);
    }
    
    println!("{}", x);  // 100
}
```

Calling an `unsafe` function requires an `unsafe` block. This makes the boundary visible.

**Safe abstractions over unsafe code.**  
You can build safe APIs on top of unsafe code:

```rust
pub struct Buffer {
    data: *mut u8,
    len: usize,
    capacity: usize,
}

impl Buffer {
    pub fn new(capacity: usize) -> Self {
        let layout = std::alloc::Layout::array::<u8>(capacity).unwrap();
        let data = unsafe {
            std::alloc::alloc(layout)
        };
        Buffer { data, len: 0, capacity }
    }
    
    pub fn push(&mut self, byte: u8) {
        assert!(self.len < self.capacity, "Buffer full");
        unsafe {
            *self.data.add(self.len) = byte;
        }
        self.len += 1;
    }
    
    pub fn get(&self, index: usize) -> Option<u8> {
        if index < self.len {
            Some(unsafe { *self.data.add(index) })
        } else {
            None
        }
    }
}

impl Drop for Buffer {
    fn drop(&mut self) {
        unsafe {
            let layout = std::alloc::Layout::array::<u8>(self.capacity).unwrap();
            std::alloc::dealloc(self.data, layout);
        }
    }
}
```

The `Buffer` API is safe—users can't violate memory safety. The `unsafe` blocks are isolated and auditable.

**Unsafe traits.**  
Some traits have safety requirements:

```rust
unsafe trait Zeroable {
    // Safe to fill with zeros
}

unsafe impl Zeroable for u32 {}
unsafe impl Zeroable for i32 {}

fn zero_out<T: Zeroable>(value: &mut T) {
    unsafe {
        std::ptr::write_bytes(value as *mut T, 0, 1);
    }
}
```

Implementing an `unsafe` trait requires `unsafe impl`. This documents that you're upholding the trait's safety contract.

**Auditing for unsafe.**  
You can search for `unsafe` to find all potentially dangerous code:

```bash
grep -r "unsafe" src/
```

This is impossible in C++—unsafe code looks like safe code.

---

## Takeaways for C++ Developers

**Mental model shift:**
- Safe by default, unsafe by opt-in (opposite of C++)
- `unsafe` blocks are explicit boundaries—you can audit them
- Unsafe code must uphold Rust's safety guarantees
- Safe abstractions can be built on unsafe foundations

**Rules of thumb:**
- Use `unsafe` only when necessary (C interop, performance, low-level code)
- Keep `unsafe` blocks small and well-documented
- Build safe APIs on top of unsafe code
- Document the safety invariants you're upholding

**Pitfalls:**
- `unsafe` doesn't disable the borrow checker—it just allows specific operations
- You're responsible for upholding safety guarantees in `unsafe` blocks
- Unsafe code can break safe code if invariants are violated
- Raw pointers don't track lifetimes—you must ensure validity manually

Rust's `unsafe` is what C++ does everywhere, but isolated and auditable. The boundary is explicit, not invisible.

---
