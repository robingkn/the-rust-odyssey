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

