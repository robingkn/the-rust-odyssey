# 2. Hello World Without the Lies

> Most Rust tutorials start with "Hello, World!" and make it look easy. They're lying by omission—Rust's complexity doesn't show up until you try to do something real.

---

## Why This Matters to a C++ Developer

In C++, you can write useful code immediately. You understand pointers, references, and RAII. You know when to use `const`, when to move, when to copy. The learning curve is smooth: start simple, add complexity as needed.

Rust doesn't work that way.

You'll hit the borrow checker on day one. Not because you're doing something exotic—because you're doing something normal. Passing a mutable reference while holding an immutable one. Storing a reference in a struct. Returning a reference to local data.

In C++, these patterns either work or fail at runtime. In Rust, they fail at compile time, with error messages that feel like the compiler is arguing with you.

This isn't a bug in Rust. It's the entire point. But it means the learning curve is inverted: you pay the cost upfront, before you've written anything useful.

---

## The C++ Mental Model

When you learn C++, you start with:
- Variables and types
- Functions and control flow
- Pointers and references (maybe)
- Classes and RAII (eventually)

You can write working code at each stage. The complexity is optional. You reach for `std::unique_ptr` when you need it, not because the language forces you to think about ownership from line one.

**Learning is incremental.**  
You add concepts as your needs grow. A beginner can write a working program without understanding move semantics. An expert uses them everywhere. Both compile.

**The compiler is permissive.**  
It warns you about potential issues, but it trusts your judgment. If you want to return a pointer to a local variable, the compiler might warn you—but it won't stop you.

**Mistakes are deferred.**  
You write code, it compiles, and you find out later whether it was correct. Segfaults, data races, and memory leaks happen at runtime, not compile time.

This model works because:
- You can prototype quickly
- You learn by doing
- The language doesn't block you while you're figuring things out

---

## Where the Model Breaks Down

Rust inverts this completely.

**You can't defer ownership decisions.**  
In C++, you can write a function that takes a pointer and figure out ownership later. In Rust, you must decide upfront: does this function borrow, take ownership, or return a reference? The compiler won't let you proceed until you answer.

**The compiler is adversarial (at first).**  
It rejects code that would compile and run fine in C++. Not because it's wrong, but because it *might* be wrong. The compiler doesn't trust you to get it right at runtime—it demands proof at compile time.

**Mistakes are immediate.**  
You don't get to run the code and see what happens. You fight the compiler until it's satisfied, then the code works. This feels backwards if you're used to iterating quickly and fixing bugs later.

The frustration is real:
- You know what you want to do
- You know it would work in C++
- The compiler says no, and the error message doesn't help

This isn't Rust being difficult for no reason. It's Rust enforcing guarantees that C++ leaves to you. But it means your first week with Rust will feel like fighting the language, not learning it.

The payoff comes later: once the code compiles, whole classes of bugs are gone. But you have to survive the first week to get there.

---

