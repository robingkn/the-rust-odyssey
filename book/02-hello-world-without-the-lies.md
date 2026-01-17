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

**The compiler feels adversarial at first.**  
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


## Rust's Model

Rust forces you to think about ownership from the start. There's no "simple mode" where you can ignore it.

**Ownership decisions are mandatory.**  
Every function parameter declares whether it takes ownership, borrows immutably, or borrows mutably. You can't defer this decision.

```rust
fn process_owned(data: String) {
    // Takes ownership, data is dropped at end
}

fn process_borrowed(data: &String) {
    // Borrows, doesn't take ownership
}

fn process_mut(data: &mut String) {
    // Mutable borrow, can modify but doesn't own
    data.push_str(" modified");
}
```

In C++, you'd use `std::string`, `const std::string&`, or `std::string&`. The difference is that Rust enforces the semantics—you can't use `data` after passing it to `process_owned`.

**The compiler is strict, not helpful (at first).**  
It rejects code that would work in C++, even if you know it's safe.

```rust
fn read_file(path: &str) -> std::io::Result<String> {
    let mut file = std::fs::File::open(path)?;
    let mut contents = String::new();
    
    // This won't compile:
    // let first_char = contents.chars().next();  // Immutable borrow
    // file.read_to_string(&mut contents)?;       // Mutable borrow - Error!
    // println!("{:?}", first_char);
    
    file.read_to_string(&mut contents)?;
    Ok(contents)
}
```

The compiler prevents you from holding an immutable reference while mutating. In C++, this would compile and might work—or might crash if the string reallocates.

**Errors are explicit.**  
Functions that can fail return `Result<T, E>`. You must handle the error or explicitly propagate it with `?`.

```rust
fn read_file(path: &str) -> std::io::Result<String> {
    let mut file = std::fs::File::open(path)?;
    let mut contents = String::new();
    file.read_to_string(&mut contents)?;
    Ok(contents)
}
```

You can't ignore the `Result`. The compiler forces you to handle it or propagate it. No silent failures.

**The learning curve is front-loaded.**  
You fight the compiler for the first week. But once you understand what it's checking, the fights become rare. The code that compiles tends to work.

---

## Takeaways for C++ Developers

**Mental model shift:**
- You can't prototype by ignoring ownership—the compiler won't let you
- Errors are values, not exceptions—you handle them explicitly
- The compiler is checking invariants you'd maintain manually in C++

**Rules of thumb:**
- Start with borrowing (`&T`) unless you need ownership
- Use `&mut T` only when you need to modify
- Use `?` to propagate errors up the call stack
- If the compiler says no, restructure your code—don't fight it

**Pitfalls:**
- Don't try to write C++ in Rust—the patterns don't translate directly
- The first week will be frustrating—this is normal
- Cloning to make the compiler happy is sometimes the right answer

The payoff comes later: once your code compiles, it usually works. The bugs you'd spend days debugging in C++ are caught at compile time.

---
