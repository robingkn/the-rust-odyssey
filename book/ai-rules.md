# AI Authoring Rules — The Rust Odyssey

This file defines the rules and constraints for AI-assisted writing
in this repository.

The book is titled:

**The Rust Odyssey: A C++ Developer’s Journey**

These rules take precedence over generic AI behavior.

---

## 1. Target Audience (Non-Negotiable)

The reader is:
- An experienced C++ developer (5–15+ years)
- Comfortable with:
  - RAII, move semantics, templates
  - Manual memory management
  - Undefined behavior and performance trade-offs
- New to Rust, but not new to systems programming

Assume high competence.
Do **not** explain basic programming concepts.

This book is **not** for:
- Programming beginners
- Rust-first learners
- Scripting-language backgrounds

---

## 2. Core Purpose of the Book

The purpose of this book is to:

> Translate **C++ mental models** into **Rust mental models**.

The book should focus on:
- Where C++ intuition transfers cleanly
- Where it breaks down
- Why Rust enforces different constraints
- What trade-offs Rust is making

Avoid framing Rust as:
- “Safer C++”
- A universal replacement for C++
- Inherently superior without cost

Honesty builds trust.

---

## 3. Writing Philosophy

### Preferred Style
- Clear, direct, pragmatic
- Slightly skeptical, never evangelical
- Engineering-focused, not academic
- Explain *why* before *how*
- Assume the reader can read code

### Avoid
- Marketing language
- Rust buzzwords without justification
- Overly friendly metaphors
- Tutorial-style step-by-step explanations

If something feels like Rust documentation, rewrite it.

---

## 4. Mandatory Chapter Structure

Unless explicitly instructed otherwise, **every chapter must follow this structure**:

1. **The C++ Intuition**  
   What an experienced C++ developer would naturally expect or attempt.

2. **Where That Intuition Breaks**  
   Why the C++ mental model does not map cleanly to Rust.

3. **Rust’s Model**  
   How Rust frames the same problem differently.

4. **Concrete Example**  
   A small but realistic example (not toy structs).

5. **Common C++ Developer Mistakes**  
   Typical incorrect assumptions or misuse patterns.

6. **Takeaway Mental Model**  
   A concise rule-of-thumb or checklist.

Consistency matters more than completeness.

---

## 5. Code Rules

### Rust Code
- Must be idiomatic and compilable
- Prefer explicitness over cleverness
- Avoid toy examples like:
  - `Point { x, y }`
  - trivial getters/setters

Prefer examples involving:
- File I/O
- Buffers and slices
- Ownership of resources
- Parsing or stateful logic
- Error propagation

### C++ Code
- Use only when it clarifies intuition
- Keep it minimal and focused
- Assume modern C++ (C++17+)

---

## 6. Perspective on Safety and Performance

When discussing safety:
- Explain *what* Rust prevents
- Explain *why* that matters
- Explain *what it costs*

When discussing performance:
- Avoid blanket claims
- Be explicit about trade-offs
- Acknowledge cases where C++ is a better fit

Never imply Rust removes the need for thinking.

---

## 7. Common Failure Modes to Watch For

Before finalizing any section, check for:
- Talking down to the reader
- Explaining syntax without motivation
- Overselling Rust’s benefits
- Hiding trade-offs
- Writing that sounds like a blog post or tutorial

If a paragraph does not help a C++ developer
**avoid a real mistake**, remove it.

---

## 8. Output Expectations

- Use Markdown
- Meaningful section headers
- Concise paragraphs
- Code blocks only when they add value

Brevity is preferred over exhaustiveness.

---

## 9. Role of AI in This Project

AI is used to:
- Improve clarity
- Tighten explanations
- Surface hidden assumptions
- Rewrite prose without changing intent

AI must **not**:
- Introduce new technical opinions
- Dilute the author’s voice
- Change the architectural intent of a chapter

When in doubt, ask for clarification or reduce scope.

---

## 10. Guiding Principle

> Respect the reader’s experience.  
> Explain the constraint.  
> Show the trade-off.  
> Leave the choice to the engineer.
