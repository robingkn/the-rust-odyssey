# Appendix A. Common C++ Pitfalls and Their Rust Counterparts

> You've learned to avoid these bugs through experience—use-after-free, iterator invalidation, data races. Rust prevents them at compile time.

---

## Why This Matters to a C++ Developer

You know the common pitfalls. You've debugged them, you've written code reviews to catch them, and you've added sanitizers to find them. These bugs are rare in well-written C++, but when they happen, they're catastrophic.

The patterns are familiar:
- Use-after-free from dangling pointers
- Iterator invalidation from container modification
- Data races from unsynchronized access
- Double-free from ownership confusion
- Null pointer dereferences

You handle these through:
- Discipline and coding standards
- Smart pointers and RAII
- Code review and pair programming
- Sanitizers (AddressSanitizer, ThreadSanitizer)
- Testing and hoping you hit the bug

This works most of the time. But the cost of missing one is high—crashes, security vulnerabilities, data corruption.

---

## The C++ Mental Model

In C++, these pitfalls are **your responsibility to avoid**.

**Use-after-free is silent.**  
You delete an object, but a pointer to it still exists. Using that pointer is undefined behavior.

```cpp
int* ptr = new int(42);
delete ptr;
*ptr = 10;  // Undefined behavior. Might work, might crash.
```

**Iterator invalidation is implicit.**  
Modifying a container can invalidate iterators. The compiler doesn't track this.

```cpp
std::vector<int> vec = {1, 2, 3};
auto it = vec.begin();
vec.push_back(4);  // May invalidate it
*it = 10;  // Undefined behavior if vec reallocated
```

**Data races are unchecked.**  
Two threads accessing the same memory, at least one writing, no synchronization—undefined behavior.

```cpp
int counter = 0;

void increment() {
    counter++;  // Data race if called from multiple threads
}
```

**Null pointers are everywhere.**  
Any pointer might be null. You have to check, or you get undefined behavior.

```cpp
Widget* w = get_widget();
w->process();  // Crash if w is null
```

---

## Where the Model Breaks Down

These bugs are **invisible to the compiler**.

**The compiler can't help.**  
It doesn't know which pointers are valid, which iterators are invalidated, or which data is shared between threads. You have to get it right every time.

**Bugs are deferred.**  
The code compiles. It might even work in testing. But in production, under load, with a specific execution order, it crashes.

**Sanitizers are incomplete.**  
AddressSanitizer catches many use-after-free bugs, but not all. ThreadSanitizer catches data races, but only if you execute the racy code path. They're tools, not guarantees.

**Scale amplifies risk.**  
In a small codebase, you know where every pointer comes from. In a large codebase, with multiple teams and years of history, these bugs slip through.

---

## Rust's Model

Rust prevents these pitfalls at compile time. Not through discipline—through the type system.

### Pitfall 1: Use-After-Free

**C++ version:**
```cpp
std::unique_ptr<Widget> w = std::make_unique<Widget>();
Widget* raw = w.get();
w.reset();
raw->process();  // Use-after-free. Compiles.
```

**Rust version:**
```rust
let w = Box::new(Widget::new());
// let raw = &*w;
drop(w);
// raw.process();  // Compile error: w doesn't live long enough
```

The compiler tracks lifetimes. You can't use a reference after the owner is dropped.

### Pitfall 2: Iterator Invalidation

**C++ version:**
```cpp
std::vector<int> vec = {1, 2, 3};
int& first = vec[0];
vec.push_back(4);
first = 10;  // Undefined behavior if vec reallocated
```

**Rust version:**
```rust
let mut vec = vec![1, 2, 3];
let first = &vec[0];
// vec.push(4);  // Compile error: can't mutate while borrowed
println!("{}", first);
```

The borrow checker prevents mutation while a reference exists.

### Pitfall 3: Data Races

**C++ version:**
```cpp
int counter = 0;

std::thread t1([&]() { counter++; });
std::thread t2([&]() { counter++; });
// Data race. Undefined behavior.
```

**Rust version:**
```rust
let mut counter = 0;

// This won't compile:
// let t1 = std::thread::spawn(|| { counter += 1; });
// Error: closure may outlive the current function, and
// `counter` cannot be shared between threads safely

// Correct version with Arc<Mutex>:
use std::sync::{Arc, Mutex};

let counter = Arc::new(Mutex::new(0));
let c1 = Arc::clone(&counter);
let c2 = Arc::clone(&counter);

let t1 = std::thread::spawn(move || {
    *c1.lock().unwrap() += 1;
});

let t2 = std::thread::spawn(move || {
    *c2.lock().unwrap() += 1;
});

t1.join().unwrap();
t2.join().unwrap();
```

The type system enforces synchronization. You can't share mutable state without a `Mutex`.

### Pitfall 4: Double-Free

**C++ version:**
```cpp
int* ptr = new int(42);
delete ptr;
delete ptr;  // Double-free. Undefined behavior.
```

**Rust version:**
```rust
let b = Box::new(42);
drop(b);
// drop(b);  // Compile error: value moved
```

The compiler prevents using a value after it's been moved/dropped.

### Pitfall 5: Null Pointer Dereference

**C++ version:**
```cpp
Widget* w = find_widget("foo");
w->process();  // Crash if w is null
```

**Rust version:**
```rust
fn find_widget(name: &str) -> Option<Widget> {
    // Returns Some(widget) or None
    None
}

let w = find_widget("foo");
// w.process();  // Compile error: Option<Widget> doesn't have process()

// Must handle None case:
match w {
    Some(widget) => widget.process(),
    None => println!("Widget not found"),
}

// Or use if let:
if let Some(widget) = w {
    widget.process();
}

// Note: If returning a reference instead of owned value:
// fn find_widget_ref(name: &str) -> Option<&Widget>
// The lifetime of the reference is tied to the source.
```

`Option<T>` makes null explicit. You must handle the `None` case.

### Pitfall 6: Use-After-Move

**C++ version:**
```cpp
auto ptr = std::make_unique<Widget>();
auto ptr2 = std::move(ptr);
ptr->process();  // Undefined behavior. Compiles.
```

**Rust version:**
```rust
let w = Box::new(Widget::new());
let w2 = w;  // w moved to w2
// w.process();  // Compile error: value moved
```

The compiler prevents using a value after it's been moved.

---

## Takeaways for C++ Developers

**Mental model shift:**
- Pitfalls you avoid through discipline are prevented by the compiler
- The borrow checker enforces what you track mentally
- `Option<T>` makes null explicit—you must handle it
- Move semantics are checked—you can't use after move

**Rust prevents at compile time:**
- Use-after-free → Lifetime tracking
- Iterator invalidation → Borrow checker
- Data races → `Send`/`Sync` traits
- Double-free → Move semantics
- Null dereference → `Option<T>`
- Use-after-move → Ownership tracking

**Rules of thumb:**
- If it compiles in Rust, these bugs are impossible (outside `unsafe`)
- The compiler errors that frustrate you are preventing bugs
- `Option<T>` is better than null pointers—handle both cases
- `Arc<Mutex<T>>` is verbose, but prevents data races

**Pitfalls:**
- The compiler won't let you write code that "might" be safe
- You must restructure code to satisfy the borrow checker
- Cloning to avoid borrow checker fights is sometimes correct
- These guarantees only apply to safe Rust—`unsafe` is your responsibility

Rust doesn't make you a better programmer. It makes the compiler catch the bugs you'd spend days debugging in C++.

---
