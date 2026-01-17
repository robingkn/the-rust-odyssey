# 11. Concurrency Without Data Races

> In C++, you use mutexes, atomics, and discipline to avoid data races. Rust makes data races a compile error—not through runtime checks, but through the type system.

---

## Why This Matters to a C++ Developer

You know how to write concurrent code. You use mutexes to protect shared state, atomics for lock-free operations, and thread-local storage to avoid sharing. You understand race conditions, deadlocks, and memory ordering.

This works through:
- Locking discipline ("Always lock before accessing shared data")
- Code review ("Did you forget to lock?")
- Thread sanitizers (ThreadSanitizer catches many races)
- Testing (and hoping you hit the race condition)

The problem is that the compiler doesn't help. It doesn't know which data is shared, which locks protect which data, or whether you've locked correctly. You have to get it right every time.

When you don't:
- Data races cause undefined behavior
- Deadlocks hang your program
- Race conditions produce incorrect results
- Bugs only appear under load, in production

These bugs are the hardest to debug. They're non-deterministic, hard to reproduce, and often disappear when you add logging or run under a debugger.

---

## The C++ Mental Model

In C++, concurrency is **manual and unchecked**.

**Shared state is implicit.**  
Any data accessible from multiple threads is potentially shared. The compiler doesn't track this. You have to know which data is shared and protect it.

```cpp
int counter = 0;  // Shared between threads? Maybe.

void increment() {
    counter++;  // Data race if called from multiple threads
}
```

**Locking is a discipline.**  
You use mutexes to protect shared data. But nothing enforces that you lock before accessing. You just have to remember.

```cpp
std::mutex mtx;
int shared_data = 0;

void update() {
    // Forgot to lock! Data race.
    shared_data++;
}
```

**Atomics are opt-in.**  
If you need lock-free access, you use `std::atomic`. But nothing stops you from accessing the same data both atomically and non-atomically, which is undefined behavior.

```cpp
std::atomic<int> counter = 0;
counter++;  // Atomic
int* ptr = &counter;
(*ptr)++;  // Non-atomic access to atomic variable. Undefined behavior.
```

**Send and Sync are implicit.**  
Some types are safe to send between threads (`std::string`), some aren't (`std::unique_ptr` to a non-thread-safe type). The compiler doesn't enforce this. You have to know.

**Data races are undefined behavior.**  
If two threads access the same memory, and at least one is writing, and there's no synchronization, you have a data race. The compiler doesn't prevent this. The behavior is undefined.

This model works when:
- You're disciplined about locking
- Code review catches mistakes
- ThreadSanitizer finds races in testing

---

## Where the Model Breaks Down

The problem is that **the compiler can't verify your locking discipline**.

**Forgotten locks.**  
You add a new function that accesses shared data. You forget to lock. The code compiles. You have a data race.

```cpp
std::mutex mtx;
int shared_data = 0;

void update() {
    std::lock_guard<std::mutex> lock(mtx);
    shared_data++;
}

void read() {
    return shared_data;  // Forgot to lock! Data race.
}
```

**Wrong locks.**  
You have multiple mutexes. You lock the wrong one. The code compiles. You have a data race.

```cpp
std::mutex mtx1, mtx2;
int data1 = 0, data2 = 0;

void update() {
    std::lock_guard<std::mutex> lock(mtx1);
    data2++;  // Locked mtx1, but accessing data2. Data race.
}
```

**Shared references.**  
You pass a reference to shared data to a thread. The thread outlives the data. The compiler doesn't stop you. You have a use-after-free.

```cpp
void spawn_thread() {
    int local = 0;
    std::thread t([&]() { local++; });  // Captures reference to local
    t.detach();
}  // local destroyed, but thread still running. Use-after-free.
```

**Atomics mixed with non-atomics.**  
You access an atomic variable non-atomically. The compiler doesn't stop you. You have undefined behavior.

**The cost is deferred.**  
You write the code, it compiles, and you find out later whether it was safe. By then, the bug is in production, and you're debugging a race condition that only happens under load.

Rust prevents data races at compile time. The type system tracks which types can be sent between threads (`Send`) and which can be shared between threads (`Sync`). The borrow checker ensures you can't have mutable access to shared data without synchronization.

This means:
- You can't forget to lock—the compiler enforces it
- You can't lock the wrong mutex—the type system tracks it
- You can't share references unsafely—the lifetime checker prevents it

The trade-off is that you have to structure your code to satisfy the compiler. But once it compiles, data races are impossible (outside `unsafe` blocks).

---


## Rust's Model

Rust prevents data races at compile time through the type system. Two traits—`Send` and `Sync`—encode thread safety, and the borrow checker enforces it.

**Send and Sync traits.**  
- `Send`: A type can be transferred between threads
- `Sync`: A type can be shared between threads (via `&T`)

Most types are `Send` and `Sync` automatically. The compiler derives them based on the fields.

```rust
struct Config {
    host: String,    // Send + Sync
    port: u16,       // Send + Sync
}
// Config is automatically Send + Sync
```

Types that aren't thread-safe don't implement these traits:

```rust
use std::rc::Rc;

let data = Rc::new(vec![1, 2, 3]);
// std::thread::spawn(move || {
//     println!("{:?}", data);  // Compile error: Rc is not Send
// });
```

**Arc for shared ownership across threads.**  
Use `Arc` (atomic reference counting) for shared ownership:

```rust
use std::sync::Arc;
use std::thread;

let data = Arc::new(vec![1, 2, 3, 4, 5]);

let handles: Vec<_> = (0..3).map(|i| {
    let data = Arc::clone(&data);
    thread::spawn(move || {
        println!("Thread {}: {:?}", i, data);
    })
}).collect();

for handle in handles {
    handle.join().unwrap();
}
```

`Arc` is `Send` and `Sync`. The compiler ensures you can't share non-thread-safe types.

**Mutex for shared mutable state.**  
Use `Mutex` to protect shared mutable data:

```rust
use std::sync::{Arc, Mutex};
use std::thread;

let counter = Arc::new(Mutex::new(0));

let handles: Vec<_> = (0..10).map(|_| {
    let counter = Arc::clone(&counter);
    thread::spawn(move || {
        let mut num = counter.lock().unwrap();
        // Note: lock() returns Result because the mutex can be "poisoned"
        // if a thread panicked while holding the lock. unwrap() is
        // acceptable here because we're not doing anything that can panic.
        *num += 1;
    })
}).collect();

for handle in handles {
    handle.join().unwrap();
}

println!("Result: {}", *counter.lock().unwrap());
```

The `Mutex` ensures exclusive access. You can't access the data without locking—the type system enforces it.

**The borrow checker prevents data races.**  
You can't have mutable access to shared data without synchronization:

```rust
let mut data = vec![1, 2, 3];

// This won't compile:
// thread::spawn(|| {
//     data.push(4);  // Error: can't capture mutable reference
// });
```

The compiler prevents you from sharing mutable references across threads.

**RwLock for read-heavy workloads.**  
Use `RwLock` when you have many readers and few writers:

```rust
use std::sync::{Arc, RwLock};
use std::thread;

let data = Arc::new(RwLock::new(vec![1, 2, 3]));

// Multiple readers:
let handles: Vec<_> = (0..5).map(|i| {
    let data = Arc::clone(&data);
    thread::spawn(move || {
        let read = data.read().unwrap();
        println!("Reader {}: {:?}", i, *read);
    })
}).collect();

// One writer:
let data_clone = Arc::clone(&data);
let writer = thread::spawn(move || {
    let mut write = data_clone.write().unwrap();
    write.push(4);
});

for handle in handles {
    handle.join().unwrap();
}
writer.join().unwrap();
```

**Channels for message passing.**  
Use channels to communicate between threads:

```rust
use std::sync::mpsc;
use std::thread;

let (tx, rx) = mpsc::channel();

thread::spawn(move || {
    let messages = vec!["hello", "from", "thread"];
    for msg in messages {
        tx.send(msg).unwrap();
    }
});

for received in rx {
    println!("Got: {}", received);
}
```

The type system ensures you can't send non-`Send` types through channels.

**Atomic types for lock-free operations.**  
Use atomics for simple lock-free operations:

```rust
use std::sync::atomic::{AtomicUsize, Ordering};
use std::sync::Arc;
use std::thread;

let counter = Arc::new(AtomicUsize::new(0));

let handles: Vec<_> = (0..10).map(|_| {
    let counter = Arc::clone(&counter);
    thread::spawn(move || {
        counter.fetch_add(1, Ordering::SeqCst);
    })
}).collect();

for handle in handles {
    handle.join().unwrap();
}

println!("Result: {}", counter.load(Ordering::SeqCst));
```

---

## Takeaways for C++ Developers

**Mental model shift:**
- Thread safety is encoded in the type system (`Send`, `Sync`)
- The compiler prevents data races—you can't forget to lock
- Shared ownership requires `Arc` (explicit atomic reference counting)
- Mutable shared state requires `Mutex` or `RwLock`

**Rules of thumb:**
- Use `Arc` for shared ownership across threads
- Use `Mutex` for shared mutable state
- Use `RwLock` for read-heavy workloads
- Use channels for message passing between threads
- Use atomics for simple lock-free operations

**Pitfalls:**
- `Rc` is not thread-safe—use `Arc` instead
- `RefCell` is not thread-safe—use `Mutex` instead
- The compiler prevents data races, not deadlocks or race conditions

Rust eliminates data races at compile time. The cost is that you must structure your code to satisfy the type system.

---
