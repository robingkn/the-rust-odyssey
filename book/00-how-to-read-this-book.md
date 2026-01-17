# How to Read This Book

This book is structured to build mental models incrementally. Each chapter assumes you've internalized the previous ones.

---

## Chapter Progression

**Chapters 1–3: Core Mental Models**  
These chapters establish the foundational concepts: ownership, borrowing, and lifetimes. If you skip these, the rest of the book won't make sense. Even if you've read Rust documentation before, read these chapters. They're written specifically to map C++ intuition to Rust's model.

**Chapters 4–7: Practical Patterns**  
These chapters show how Rust's core concepts apply to real-world problems: RAII, error handling, and resource management. This is where you'll start to see why Rust's constraints exist.

**Chapters 8–11: Advanced Topics**  
These chapters cover traits, zero-cost abstractions, unsafe Rust, and concurrency. They assume you're comfortable with the borrow checker and are ready to see how Rust's design enables performance without sacrificing safety.

**Chapter 12: When Rust Feels Hard**  
This chapter addresses the frustration you'll inevitably feel. It's not a pep talk—it's a practical guide to recognizing when you're fighting the language and when the language is preventing a real bug.

---

## The Role of Appendices

The appendices are reference material, not required reading:

- **Appendix A** catalogs common C++ pitfalls and shows how Rust prevents them
- **Appendix B** teaches you how to read Rust compiler errors (which are more helpful than you expect)
- **Appendix C** provides a mental model cheat sheet for quick reference

Use them when you're stuck, not as a substitute for the main chapters.

---

## Patience with Early Friction

The first three chapters will feel slow. You'll be tempted to skim, to jump ahead, to start writing code before you understand the model.

Don't.

Rust's borrow checker is unforgiving if you don't understand why it exists. The time you spend building the right mental model in Chapters 1–3 will save you hours of frustration later.

If you find yourself fighting the compiler repeatedly, go back and reread Chapter 3 (Ownership) and Chapter 5 (Lifetimes). The problem is almost always a gap in your mental model, not a limitation of the language.

---

## Do Not Translate Mechanically

The biggest mistake C++ developers make when learning Rust is trying to translate C++ patterns directly.

This doesn't work.

Rust is not "C++ with a stricter compiler." It's a different set of trade-offs, enforced at compile time. Patterns that make sense in C++—like storing raw pointers in a container, or using inheritance for polymorphism—don't map cleanly to Rust.

Instead of asking "How do I do X in Rust?" ask "Why does Rust make X difficult?" The answer will usually reveal a better approach.

---

## Code Examples

The code examples in this book are small and focused. They're designed to illustrate a single concept, not to be production-ready.

If an example feels contrived, that's intentional. Real-world code has too many moving parts to isolate the concept being taught. Once you understand the concept, you'll recognize it in larger codebases.

All Rust code in this book compiles on stable Rust. If an example doesn't compile, that's a bug—report it.

---

## How to Use This Book

**If you're evaluating Rust for a project:**  
Read Chapters 1–3 and Chapter 12. That will give you enough context to understand the trade-offs.

**If you're committed to learning Rust:**  
Read the book front-to-back. Do not skip chapters. The mental models build on each other.

**If you're stuck on a specific problem:**  
Check the appendices first. If that doesn't help, reread the chapter that introduced the concept you're struggling with.

---

## What This Book Won't Teach You

This book will not teach you:
- How to set up a Rust development environment
- How to use Cargo (Rust's build tool)
- How to write idiomatic Rust for every use case
- How to integrate Rust with existing C++ codebases

For those topics, consult the official Rust documentation and ecosystem-specific guides.

This book teaches you how to think in Rust. The rest is syntax.

