# 9. Zero-Cost Abstractions (For Real)

> C++ promises zero-cost abstractions—but you know the reality. Virtual functions have vtable overhead, templates bloat binaries, and RAII has hidden costs. Rust makes the same promise, with different trade-offs.

---

## Why This Matters to a C++ Developer

You know that "zero-cost" doesn't mean "free." It means you don't pay for what you don't use, and you couldn't hand-write faster code.

In C++, this works most of the time:
- Inline functions have no call overhead
- Templates generate specialized code
- RAII destructors are deterministic
- Move semantics avoid copies

But there are costs:
- Virtual functions require vtable lookups
- Templates increase binary size
- Exception handling adds overhead (even when not thrown)
- Shared pointers have atomic reference counting

You accept these costs because the alternatives are worse. Manual memory management is error-prone. Writing specialized code for every type is unmaintainable. Avoiding abstractions makes code fragile.

The question isn't whether abstractions have cost—it's whether the cost is predictable and acceptable.

---

## The C++ Mental Model

In C++, zero-cost abstractions are **mostly true, with exceptions**.

**Inlining eliminates overhead.**  
Small functions are inlined. The abstraction disappears at compile time. You write readable code, and the compiler generates efficient machine code.

```cpp
inline int square(int x) { return x * x; }
// Compiles to a single multiply instruction
```

**Templates are compile-time.**  
Generic code is specialized for each type. No runtime dispatch, no type erasure. The abstraction is resolved at compile time.

```cpp
template<typename T>
T max(T a, T b) { return a > b ? a : b; }
// Generates specialized code for each type
```

**RAII is deterministic.**  
Destructors run at scope exit. No garbage collection, no unpredictable pauses. You know exactly when cleanup happens.

**Move semantics avoid copies.**  
You transfer ownership instead of copying. The abstraction (unique_ptr, vector) is as efficient as manual memory management.

But there are exceptions:

**Virtual functions have overhead.**  
Dynamic dispatch requires a vtable lookup. You pay for polymorphism at runtime.

```cpp
virtual void process() = 0;  // Vtable lookup on every call
```

**Exceptions have hidden costs.**  
Even if you never throw, exception handling adds code size and complexity. Some codebases disable exceptions entirely.

**Shared pointers are expensive.**  
Atomic reference counting has overhead. Every copy, every assignment, every destruction touches shared state.

This model works because:
- Most abstractions are zero-cost
- The costs that exist are predictable
- You can opt out when performance matters

---

## Where the Model Breaks Down

The problem is that **the costs are not always obvious**.

**Binary bloat from templates.**  
Every template instantiation generates new code. If you instantiate `std::vector` with 20 types, you get 20 copies of the vector implementation. This increases binary size and compile time.

**Inline functions aren't always inlined.**  
The compiler decides. If a function is too large, or called through a pointer, it won't be inlined. You don't know until you check the assembly.

**Exception overhead is always present.**  
Even if you never throw, the compiler generates unwinding tables and exception-handling code. This adds to binary size and can affect optimization.

**Virtual functions prevent optimization.**  
The compiler can't inline through a virtual call. It can't devirtualize unless it can prove the exact type. Dynamic dispatch limits what the optimizer can do.

**Shared pointers are slower than you think.**  
Atomic operations are expensive. If you're copying `shared_ptr` in a tight loop, you're paying for synchronization on every copy.

**Move semantics aren't free.**  
Moving is cheaper than copying, but it's not free. A moved-from object is in a valid-but-unspecified state. You still have to check, you still have to handle it.

Rust makes the same zero-cost promise, but with different trade-offs:
- No virtual functions (use trait objects, which are explicit)
- No exceptions (use `Result`, which is explicit)
- No shared pointers by default (use `Rc` or `Arc`, which are explicit)
- Ownership prevents hidden copies

The costs in Rust are more visible. When you pay for something, you know it. When you don't pay, the compiler guarantees it.

---


## Rust's Model

Rust makes the same zero-cost promise as C++, but with different trade-offs. The costs are more visible, and the compiler provides stronger guarantees.

**Monomorphization for generics.**  
Generic functions are specialized for each type, just like C++ templates:

```rust
fn max<T: PartialOrd>(a: T, b: T) -> T {
    if a > b { a } else { b }
}

// Generates specialized code for each type:
let x = max(1, 2);        // max::<i32>
let y = max(1.0, 2.0);    // max::<f64>
```

No runtime dispatch. The abstraction disappears at compile time.

**Trait objects for dynamic dispatch.**  
When you need polymorphism, use trait objects. The cost is explicit in the type:

```rust
trait Draw {
    fn draw(&self);
}

struct Circle { radius: f64 }
struct Square { side: f64 }

impl Draw for Circle {
    fn draw(&self) { println!("Drawing circle"); }
}

impl Draw for Square {
    fn draw(&self) { println!("Drawing square"); }
}

// Static dispatch (monomorphization):
fn draw_static<T: Draw>(shape: &T) {
    shape.draw();  // No vtable lookup
}

// Dynamic dispatch (trait object):
fn draw_dynamic(shape: &dyn Draw) {
    shape.draw();  // Vtable lookup
}
```

Static dispatch is zero-cost. Dynamic dispatch has vtable overhead, but it's explicit in the signature.

**Iterators are zero-cost.**  
Rust iterators compile to the same code as hand-written loops:

```rust
let numbers = vec![1, 2, 3, 4, 5];

// High-level iterator chain:
let sum: i32 = numbers.iter()
    .filter(|&&x| x % 2 == 0)
    .map(|&x| x * 2)
    .sum();

// Compiles to the same code as:
let mut sum = 0;
for &x in &numbers {
    if x % 2 == 0 {
        sum += x * 2;
    }
}
```

The abstraction is free. The compiler optimizes iterator chains into tight loops.

**No hidden allocations.**  
Rust doesn't allocate unless you explicitly use heap types:

```rust
// Stack-allocated:
let array = [1, 2, 3, 4, 5];

// Heap-allocated (explicit):
let vec = vec![1, 2, 3, 4, 5];

// No hidden copies:
fn process(data: Vec<i32>) {
    // Takes ownership, no copy
}
```

Unlike C++ `std::vector`, Rust's `Vec` doesn't copy on assignment—it moves. Copies are explicit with `.clone()`.

**Inline and const evaluation.**  
Small functions are inlined, and const functions are evaluated at compile time:

```rust
#[inline]
fn square(x: i32) -> i32 {
    x * x
}

const fn factorial(n: u32) -> u32 {
    match n {
        0 => 1,
        _ => n * factorial(n - 1),
    }
}

const FACT_5: u32 = factorial(5);  // Computed at compile time
```

**No exceptions overhead.**  
Rust doesn't have exceptions. No unwinding tables, no hidden code size cost. Error handling with `Result` is explicit and zero-cost (when inlined).

**Reference counting is explicit.**  
Shared ownership requires `Rc` or `Arc`. The cost is visible in the type:

```rust
use std::rc::Rc;

let data = Rc::new(vec![1, 2, 3]);
let data2 = Rc::clone(&data);  // Explicit reference count increment
```

Unlike C++ `shared_ptr`, you can't accidentally create shared ownership. You have to explicitly use `Rc` or `Arc`.

---

## Takeaways for C++ Developers

**Mental model shift:**
- Generics are monomorphized—zero-cost, but increases binary size
- Trait objects are explicit dynamic dispatch—cost is visible in the type
- Iterators are zero-cost abstractions—compile to tight loops
- No hidden allocations or copies—everything is explicit

**Rules of thumb:**
- Use generics (`T: Trait`) for static dispatch (zero-cost)
- Use trait objects (`&dyn Trait`) for dynamic dispatch (vtable cost)
- Iterators are as fast as hand-written loops—use them
- `Rc`/`Arc` are explicit reference counting—use only when needed

**Pitfalls:**
- Monomorphization increases binary size—each instantiation generates code
- Trait objects can't be sized—use `Box<dyn Trait>` or `&dyn Trait`
- `Rc` is not thread-safe—use `Arc` for concurrent access
- Cloning is explicit—no hidden copies like C++ copy constructors

Rust's zero-cost abstractions are more explicit than C++. When you pay for something, you know it. When you don't, the compiler guarantees it.

---
