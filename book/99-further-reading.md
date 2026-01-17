# Further Reading

This book teaches mental models, not comprehensive Rust. Here's where to go next.

---

## Official Rust Resources

**The Rust Programming Language** (The Book)  
https://doc.rust-lang.org/book/  
The official introduction to Rust. Comprehensive, well-written, assumes no systems programming background. Read this for syntax and standard library details this book skipped.

**Rust by Example**  
https://doc.rust-lang.org/rust-by-example/  
Code-first learning. Useful for seeing patterns in action without lengthy explanations.

**The Rustonomicon**  
https://doc.rust-lang.org/nomicon/  
The unsafe Rust guide. Read this if you're implementing low-level data structures or interfacing with C. Assumes you understand why unsafe exists.

**Rust Standard Library Documentation**  
https://doc.rust-lang.org/std/  
The reference. Well-documented, with examples. Use this when you need to know what methods a type provides.

**Rust Reference**  
https://doc.rust-lang.org/reference/  
The language specification. Dry, precise, complete. Read this when you need to know exactly what the language guarantees.

---

## Specialized Topics

**Asynchronous Programming in Rust**  
https://rust-lang.github.io/async-book/  
Covers async/await, futures, and the async runtime ecosystem. Essential if you're building network services or I/O-heavy applications.

**The Cargo Book**  
https://doc.rust-lang.org/cargo/  
Everything about Rust's build system and package manager. Read this when you need to configure builds, manage dependencies, or publish crates.

**Rust API Guidelines**  
https://rust-lang.github.io/api-guidelines/  
Best practices for designing Rust APIs. Useful if you're writing libraries others will use.

---

## C++ to Rust Transition

**Rust for C++ Programmers** (GitHub)  
https://github.com/nrc/r4cppp  
Nick Cameron's guide. Covers similar ground to this book with different examples and explanations. Worth reading for alternative perspectives.

**Comparing Rust and C++** (Various blog posts)  
Search for "Rust vs C++" on engineering blogs. Many experienced C++ developers have documented their learning process. The best posts focus on trade-offs, not advocacy.

---

## What Not to Read

Avoid:
- "Why Rust is better than X" articles—they oversimplify
- Tutorials that skip explaining why the borrow checker exists
- Advocacy pieces that ignore Rust's costs
- Anything that promises Rust will make you a better programmer

Rust is a tool. Learn it by using it, not by reading about it.

---

## Next Steps

1. Write Rust code. Start small: command-line tools, parsers, file processors.
2. Fight the borrow checker. Understand why it rejects your code.
3. Read the compiler errors. They're teaching you.
4. Refactor working code. See how Rust's guarantees enable confident changes.
5. Contribute to an existing Rust project. See how real codebases are structured.

The mental models in this book are a foundation. The rest is practice.

