# 1. Why Rust Exists (Through a C++ Lens)

> You've spent years mastering C++, learning when to use `unique_ptr` vs raw pointers, when move semantics matter, and how to avoid undefined behavior. Rust claims to solve problems you've learned to work around—but at what cost?

---

## Why This Matters to a C++ Developer

C++ gives you complete control. You manage memory, choose your abstractions, and pay only for what you use. When things go wrong, you debug, add assertions, run sanitizers, or tighten code review.

This works. Millions of lines of production C++ prove it.

But it requires:
- Discipline across teams and years
- Tooling that catches problems after the fact
- Conventions that aren't enforced by the language
- Expertise that doesn't scale with codebase size

The larger the team, the longer the project lives, the more fragile this becomes. Not because C++ is broken, but because it trusts you completely—even when you're tired, distracted, or onboarding someone new.

Rust doesn't trust you. It makes that lack of trust explicit, compile-time, and non-negotiable.

---

## The C++ Mental Model

As a C++ developer, you think in terms of:

**Ownership by convention**  
You know who owns a pointer. It's in the variable name (`raw_ptr`, `owner_ptr`), the comment, or the team wiki. The compiler doesn't enforce it—you do.

**Safety through discipline**  
You avoid dangling pointers by being careful. You use RAII. You follow the rule of three/five. You run Valgrind. You write tests.

**Performance through control**  
You choose when to copy, when to move, when to allocate. The language doesn't second-guess you. If you want a raw pointer into a vector's buffer, you get one—and you're responsible for not invalidating it.

**Flexibility over guarantees**  
C++ lets you do anything. Cast away `const`, reinterpret memory, ignore return values. The language assumes you know what you're doing. When you don't, the bug is yours to find.

This mental model works when:
- The team is small and experienced
- Code review catches mistakes
- Tooling fills the gaps
- The codebase doesn't outlive institutional knowledge

---

## Where the Model Breaks Down

The problem isn't that C++ allows unsafe code. The problem is that **safe and unsafe code look identical**.

```cpp
std::vector<int> vec = {1, 2, 3};
int* ptr = &vec[0];
vec.push_back(4);  // ptr is now dangling
// No warning. No error. Just undefined behavior.
```

The compiler can't help you here. It doesn't track:
- Whether a pointer is still valid
- Whether two threads are accessing the same memory
- Whether you've moved from an object and then used it again

You catch these bugs through:
- Code review (if the reviewer notices)
- Sanitizers (if you run them, and they trigger)
- Production crashes (if you're unlucky)

At scale, this doesn't work:
- A refactor in one module breaks assumptions in another
- A threading bug appears only under load
- A use-after-free hides in a rarely-executed path

The cost isn't the bug itself—it's the **time to find it**, the **confidence lost**, and the **features not shipped** because you're debugging memory corruption.

C++ gives you the tools to write safe code. It doesn't stop you from writing unsafe code. Rust inverts that: it stops you from writing unsafe code unless you explicitly opt out.

That's not a value judgment. It's a trade-off. The question is whether that trade-off makes sense for your project.

---


## Rust's Model

Rust makes safety the default, not the exception. The compiler enforces what C++ leaves to discipline.

**Ownership is explicit and checked.**  
Every value has exactly one owner. When the owner goes out of scope, the value is dropped. The compiler tracks this and prevents use-after-free at compile time.

```rust
fn process_data() {
    let data = vec![1, 2, 3];
    consume(data);
    // println!("{:?}", data);  // Compile error: value moved
}

fn consume(v: Vec<i32>) {
    // Use v here
}  // v is dropped here
```

The compiler knows `data` was moved into `consume`. Trying to use it afterward is a compile error, not undefined behavior.

**Borrowing is tracked.**  
References have lifetimes. The compiler ensures a reference never outlives the data it points to.

```rust
fn get_first(v: &Vec<i32>) -> &i32 {
    &v[0]  // Lifetime tied to v
}

// This won't compile:
// fn dangling() -> &i32 {
//     let v = vec![1, 2, 3];
//     &v[0]  // Error: v doesn't live long enough
//            // (The reference would outlive v - see Chapter 5)
// }
```

The compiler rejects code that would create dangling references. Not at runtime—at compile time.

**Mutability and aliasing are exclusive.**  
You can have multiple immutable references, or one mutable reference, but not both. This prevents iterator invalidation and data races.

```rust
let mut vec = vec![1, 2, 3];
let first = &vec[0];
// vec.push(4);  // Compile error: can't mutate while borrowed
println!("{}", first);
```

The compiler enforces this. You can't accidentally invalidate a reference by modifying the container.

**The trade-off is upfront complexity.**  
You must satisfy the compiler before your code runs. This is slower when prototyping, but eliminates entire classes of bugs. The cost is paid once, at compile time, not repeatedly in production.

---

## Takeaways for C++ Developers

**Mental model shift:**
- Ownership is not a convention—it's enforced by the compiler
- References have lifetimes that the compiler tracks
- Mutability and aliasing cannot coexist

**Rules of thumb:**
- If you're passing ownership, use move semantics (default in Rust)
- If you're borrowing, use references (`&T` or `&mut T`)
- If the compiler rejects your code, it's preventing a bug you'd find later in C++

**Pitfalls:**
- Fighting the compiler means you're trying to do something unsafe
- The borrow checker isn't wrong—it's enforcing invariants you'd maintain manually in C++
- Cloning to satisfy the compiler isn't always wrong—sometimes it's the correct solution (especially for small types or when simplicity matters more than performance)

Rust doesn't make you a better programmer. It makes the compiler enforce what you already know you should do.

---
