# 4. Borrowing vs Aliasing

> In C++, you pass references freely—const or mutable, it doesn't matter. Rust treats aliasing and mutability as mutually exclusive, and suddenly your intuition about references stops working.

---

## Why This Matters to a C++ Developer

You use references constantly. They're cheap, they avoid copies, and they're safer than pointers (usually). You pass `const T&` when you don't need to modify, and `T&` when you do.

The rules are simple:
- `const T&`: Read-only access
- `T&`: Read-write access
- Multiple `const T&` references? Fine.
- Multiple `T&` references? Also fine, just be careful.

"Be careful" means:
- Don't invalidate references by reallocating
- Don't create data races in multithreaded code
- Don't modify through one reference while reading through another

These are conventions. The compiler doesn't enforce them. When you violate them, you get undefined behavior—sometimes immediately, sometimes much later.

In practice, this works. You learn the patterns, you avoid the pitfalls, and you move on. Until you don't, and you spend a day debugging why a reference suddenly points to garbage.

---

## The C++ Mental Model

In C++, references are **aliases**. They're just another name for the same object.

**Aliasing is unrestricted.**  
You can have as many references to the same object as you want. Const or mutable, it doesn't matter—the compiler lets you create them.

```cpp
std::vector<int> vec = {1, 2, 3};
const int& a = vec[0];
const int& b = vec[0];
int& c = vec[0];
// All valid. All aliases to the same memory.
```

**Mutability is a property of the reference.**  
A `const T&` means *you* can't modify it through *this* reference. It doesn't mean the object is immutable—someone else might have a mutable reference to the same object.

```cpp
const int& readonly = vec[0];
vec[0] = 42;  // Modifies the object readonly refers to. Legal.
```

**Invalidation is your problem.**  
If you hold a reference and the container reallocates, the reference becomes dangling. The compiler doesn't track this. You just have to know.

```cpp
std::vector<int> vec = {1, 2, 3};
int& ref = vec[0];
vec.push_back(4);  // May reallocate, invalidating ref
// ref is now dangling. No warning.
```

**Concurrency is manual.**  
If two threads access the same object, and at least one is writing, you have a data race. The compiler doesn't prevent this. You use mutexes, atomics, or thread-local storage.

This model is flexible:
- You can alias freely
- You can mutate through any non-const reference
- You manage the consequences yourself

---

## Where the Model Breaks Down

The problem is that **aliasing and mutation interact in subtle ways**.

**Iterator invalidation.**  
You hold a reference into a container. Someone modifies the container. Your reference is now invalid. The compiler doesn't know. Your code compiles. You get undefined behavior.

```cpp
std::vector<int> vec = {1, 2, 3};
int& first = vec[0];
vec.clear();
first = 10;  // Undefined behavior. Compiles fine.
```

**Unintended mutation.**  
You pass a `const T&` to a function, assuming the object won't change. But someone else has a mutable reference to the same object. The function reads inconsistent state.

```cpp
void process(const std::vector<int>& vec) {
    int first = vec[0];
    // Someone else modifies vec here (via another reference)
    int second = vec[0];
    // first != second, even though vec is "const"
}
```

**Data races.**  
Two threads access the same object. One reads, one writes. No synchronization. Undefined behavior. The compiler doesn't stop you.

The cost of this flexibility is vigilance:
- You must track which references are live
- You must know when containers might reallocate
- You must synchronize access in multithreaded code

Rust makes a different trade-off: it restricts aliasing to prevent these bugs. You can have multiple immutable references, or one mutable reference, but not both. The compiler enforces this at compile time.

This feels limiting—because it is. But it eliminates iterator invalidation, unintended mutation, and data races. Not through discipline, but through the type system.

---


## Rust's Model

Rust enforces a simple rule: you can have multiple immutable references, or one mutable reference, but not both at the same time.

**Aliasing XOR mutability.**  
This is the core of Rust's safety guarantee. You can alias (multiple references), or you can mutate, but not both.

```rust
let mut vec = vec![1, 2, 3];

// Multiple immutable borrows: OK
let r1 = &vec;
let r2 = &vec;
println!("{:?} {:?}", r1, r2);

// One mutable borrow: OK (after immutable borrows end)
let r3 = &mut vec;
r3.push(4);

// But not both:
// let r4 = &vec;
// let r5 = &mut vec;  // Compile error: can't borrow mutably while borrowed immutably
```

This prevents iterator invalidation at compile time:

```rust
let mut vec = vec![1, 2, 3];
let first = &vec[0];
// vec.push(4);  // Compile error: can't mutate while borrowed
println!("{}", first);
```

In C++, this would compile and might crash. In Rust, it's a compile error.

**Borrows have lifetimes.**  
The compiler tracks how long a borrow is valid. A borrow cannot outlive the data it references.

```rust
fn process_data() {
    let vec = vec![1, 2, 3];
    let first = &vec[0];
    println!("{}", first);
}  // vec and first both dropped here

// This won't compile:
// fn get_first() -> &i32 {
//     let vec = vec![1, 2, 3];
//     &vec[0]  // Error: vec doesn't live long enough
// }
```

**Interior mutability is explicit.**  
If you need to mutate through a shared reference, you use `Cell<T>` or `RefCell<T>`. The cost is visible in the type.

```rust
use std::cell::RefCell;

struct Cache {
    data: RefCell<Vec<String>>,
}

impl Cache {
    fn add(&self, item: String) {
        // Mutate through shared reference
        self.data.borrow_mut().push(item);
    }
    
    fn get(&self, index: usize) -> Option<String> {
        self.data.borrow().get(index).cloned()
    }
}
```

`RefCell` checks borrowing rules at runtime. If you violate them (e.g., borrow mutably while already borrowed), the program panics. This is explicit—you know where the runtime checks are.

**Slices prevent invalidation.**  
Instead of holding a reference into a container, you use slices that borrow the data.

```rust
fn process_slice(data: &[i32]) {
    for item in data {
        println!("{}", item);
    }
}

fn main() {
    let vec = vec![1, 2, 3, 4, 5];
    process_slice(&vec[1..4]);  // Borrow a slice
    // vec is still valid here
}
```

The slice borrows from `vec`, so you can't modify `vec` while the slice exists. The compiler enforces this.

---

## Takeaways for C++ Developers

**Mental model shift:**
- Aliasing and mutability are mutually exclusive—enforced by the compiler
- References have lifetimes—the compiler tracks them
- Interior mutability requires explicit types (`Cell`, `RefCell`)
- Iterator invalidation is impossible (outside `unsafe`)

**Rules of thumb:**
- Use `&T` for shared, read-only access
- Use `&mut T` for exclusive, mutable access
- You can't hold a reference while modifying the container
- If you need shared mutability, use `RefCell` (single-threaded) or `Mutex` (multi-threaded)

**Pitfalls:**
- You can't store a reference and modify the source—restructure your code
- `RefCell` checks at runtime—panics if you violate borrowing rules
- Cloning to avoid borrow checker fights is sometimes correct
- The compiler's "cannot borrow as mutable" errors mean you're trying to alias and mutate

Rust's borrowing rules eliminate data races and iterator invalidation. The cost is that you must structure your code to satisfy the compiler.

---
