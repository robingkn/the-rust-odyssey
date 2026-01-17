# Preface

This book exists because learning Rust as an experienced C++ developer is harder than it should be.

Not because Rust is poorly designed, but because most Rust learning materials assume you're either new to systems programming or coming from a garbage-collected language. If you've spent years with C++—managing lifetimes manually, reasoning about move semantics, debugging memory corruption—you already understand the problems Rust solves. What you need is a translation layer between the mental models you've built and the ones Rust enforces.

That's what this book provides.

---

## Who This Book Is For

This book is written for experienced C++ developers. Specifically:

- You have 5+ years of professional C++ experience
- You're comfortable with RAII, move semantics, and templates
- You've debugged use-after-free, iterator invalidation, and data races
- You understand the trade-offs between performance and safety
- You're curious about Rust but skeptical of the hype

If you've never written C++, this book will not make sense. If you're new to systems programming, start elsewhere.

---

## Who This Book Is Not For

This is not:

- An introduction to programming
- A comprehensive Rust reference
- A guide to building production systems in Rust
- An argument that Rust is better than C++

If you're looking for a tutorial that walks you through syntax step-by-step, read *The Rust Programming Language* (the official book). If you want to understand why Rust makes the design choices it does—and how those choices map to problems you've already solved in C++—keep reading.

---

## What This Book Teaches

This book focuses on mental models, not syntax.

Each chapter takes a concept you already understand in C++ and shows:
- Where your C++ intuition transfers cleanly
- Where it breaks down
- Why Rust enforces different constraints
- What trade-offs Rust is making

The goal is not to make you love Rust. The goal is to make you understand it well enough to decide whether it's the right tool for your problem.

---

## What to Expect

Rust will feel restrictive at first. The borrow checker will reject code that would compile in C++. You'll be tempted to fight it, to find workarounds, to conclude that Rust is too rigid for real-world use.

That frustration is normal. It's also temporary.

The borrow checker isn't rejecting your code arbitrarily. It's enforcing invariants you'd maintain manually in C++—invariants that, when violated, cause the bugs you've spent hours debugging. The difference is that Rust makes those invariants explicit and checks them at compile time.

This book won't eliminate that friction, but it will explain why the friction exists and what you gain from it.

---

## A Note on Tone

This book is not evangelical. Rust is not a silver bullet. It makes trade-offs, and those trade-offs won't make sense for every project.

If you're working on a codebase with millions of lines of C++, a team that knows C++ deeply, and tooling that catches most memory bugs, Rust might not be worth the migration cost. If you're starting a new project where memory safety bugs are expensive and hard to debug, Rust might be exactly what you need.

The book presents the trade-offs. You make the call.

---

## Acknowledgments

This book would not exist without the Rust community's documentation, the C++ community's decades of hard-won knowledge, and the engineers who've debugged enough memory corruption to appreciate what Rust prevents.

