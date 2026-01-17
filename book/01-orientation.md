# Orientation for the C++ Developer

This chapter sets expectations for the journey ahead.
It is not about syntax or features, but about mindset.

Rust will feel familiar in places—and deeply uncomfortable in others.
That discomfort is intentional.

---

## 1. The C++ Intuition

As a C++ developer, you are used to:
- Explicit control over memory and lifetimes
- Paying for what you use
- Choosing between safety, performance, and flexibility
- Taking responsibility for correctness

You already know that:
- Undefined behavior is real
- Abstractions leak
- Tooling cannot save you from bad design

From this perspective, Rust often looks unnecessary:
> “I already know how to write safe C++.”

That intuition is reasonable—and incomplete.

---

## 2. Where That Intuition Breaks

Modern C++ relies heavily on:
- Conventions
- Code review
- Discipline
- Post-facto tools (sanitizers, fuzzers, static analysis)

Despite best efforts, large C++ systems still suffer from:
- Aliasing-related bugs
- Lifetime mismatches
- Use-after-free errors
- Subtle data races

The problem is not lack of skill.
The problem is that **C++ allows expressing states the system cannot reason about**.

C++ gives you freedom first, and asks you to be careful later.

Rust inverts this order.

---

## 3. Rust’s Model

Rust assumes that:
- Humans are fallible
- Codebases grow beyond original authors
- Correctness must be enforced structurally

Instead of trusting discipline, Rust encodes rules directly into the type system:
- Who owns what
- Who may access it
- Under what conditions mutation is allowed

These rules are not optional.
They are checked at compile time, before the program exists.

This is not about making programmers safer.
It is about making **illegal states unrepresentable**.

---

## 4. A Concrete Contrast (Conceptual)

In C++, ownership is often:
- Implicit
- Documented informally
- Enforced socially

In Rust, ownership is:
- Explicit
- Enforced by the compiler
- Part of every API boundary

This means some designs that are trivial in C++
are intentionally difficult—or impossible—in Rust.

That friction is not accidental.

---

## 5. Common C++ Developer Reactions

Experienced C++ developers often respond with:
- “This is too restrictive”
- “I know what I’m doing”
- “The compiler is in my way”

These reactions are understandable.
They usually indicate that:
- A familiar pattern is being rejected
- The design relies on assumptions Rust refuses to accept

The key shift is realizing:
> Rust is not questioning your competence.  
> It is questioning the design’s invariants.

---

## 6. Takeaway Mental Model

- C++ optimizes for **expressiveness first**
- Rust optimizes for **correctness first**
- Both can produce fast, low-level code
- They differ in what they make easy—and what they make impossible

Rust is not here to replace your experience.
It is here to constrain it productively.

The rest of this book explores what that means in practice.
