# A Note on Examples

The code examples in this book are deliberately small and focused.

---

## Why Examples Are Small

Real-world code is messy. It has error handling, edge cases, performance optimizations, and dependencies on other modules. That complexity obscures the concept being taught.

The examples in this book strip away everything except the core idea. If you're looking at a 10-line example and thinking "this would never work in production," you're right. That's not the point.

The point is to isolate the mental model. Once you understand why Rust enforces a particular constraint in a simple example, you'll recognize that constraint in real code—and you'll know how to work with it instead of against it.

---

## Examples Are Illustrative, Not Production-Ready

None of the examples in this book are intended for production use. They:
- Omit error handling when it's not relevant to the concept
- Use simplified types to reduce cognitive load
- Ignore performance considerations that would matter in real code
- Assume a single-threaded context unless concurrency is the topic

If you copy an example into your codebase, you'll need to add:
- Proper error handling
- Input validation
- Performance optimizations
- Thread safety (if applicable)

The examples are teaching tools, not templates.

---

## Correctness Over Completeness

Every Rust example in this book compiles on stable Rust. If an example doesn't compile, that's a bug.

However, "compiles" does not mean "handles all edge cases" or "is the best way to solve the problem." It means the example demonstrates the concept without introducing unrelated complexity.

When an example shows a pattern that's suboptimal in production, the text will note it. The goal is to teach you how Rust works, not to prescribe how you should use it.

---

## C++ Examples Are Minimal

C++ examples appear only when they clarify the contrast with Rust. They are not:
- Comprehensive demonstrations of C++ best practices
- Optimized for performance
- Representative of modern C++ idioms in all cases

They exist to show where C++ and Rust diverge, and why that divergence matters.

If a C++ example looks like code you'd never write, consider whether the Rust equivalent prevents you from writing it at all. That's often the point.

---

## What to Do with Examples

**When reading:**  
Focus on the concept, not the specifics. Ask yourself: "What invariant is Rust enforcing here? How would I maintain that invariant manually in C++?"

**When experimenting:**  
Type the examples yourself. Modify them. Break them. See what the compiler says. Rust's error messages are part of the learning process.

**When applying to your code:**  
Don't copy examples directly. Use them to understand the constraint, then design your solution around that constraint.

The examples are a map, not the territory.

