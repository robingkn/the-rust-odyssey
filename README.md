# The Rust Odyssey: A C++ Developer's Journey

A technical book for experienced C++ developers learning Rust.

## What This Book Is

This book translates C++ mental models into Rust mental models. It assumes you're an experienced C++ developer—comfortable with RAII, move semantics, templates, and manual memory management—and focuses on where your intuition transfers cleanly to Rust, where it breaks down, and why Rust enforces different constraints.

Each chapter maps a C++ concept to its Rust equivalent, explains the trade-offs, and shows common mistakes C++ developers make when learning Rust.

## Who This Book Is For

- Experienced C++ developers (5-15+ years)
- Systems programmers evaluating Rust
- Engineers who understand memory safety trade-offs
- Developers skeptical of language hype

## Who This Book Is Not For

- Programming beginners
- Developers new to systems programming
- Readers looking for comprehensive Rust reference
- Anyone expecting language advocacy

## How to Read This Book

Start with the [Preface](book/00-preface.md) and [How to Read This Book](book/00-how-to-read-this-book.md).

Then read chapters in order—each builds on previous mental models.

See the full [Table of Contents](book/README.md).

## Table of Contents

### Front Matter
- [Title Page](book/00-title-page.md)
- [Copyright](book/00-copyright.md)
- [Preface](book/00-preface.md)
- [How to Read This Book](book/00-how-to-read-this-book.md)
- [A Note on Examples](book/00-note-on-examples.md)

### Part I: Why Another Systems Language?
1. [Why Rust Exists (Through a C++ Lens)](book/01-why-rust-exists.md)
2. [Hello World Without the Lies](book/02-hello-world-without-the-lies.md)

### Part II: Ownership Changes Everything
3. [Ownership: The Missing Concept in C++](book/03-ownership-the-missing-concept.md)
4. [Borrowing vs Aliasing](book/04-borrowing-vs-aliasing.md)
5. [Lifetimes Without the Fear](book/05-lifetimes-without-the-fear.md)
6. [RAII Reimagined](book/06-raii-reimagined.md)

### Part III: Expressiveness Without Cost
7. [Error Handling Without Exceptions](book/07-error-handling-without-exceptions.md)
8. [Traits vs Templates](book/08-traits-vs-templates.md)
9. [Zero-Cost Abstractions (For Real)](book/09-zero-cost-abstractions.md)

### Part IV: Power, Safety, and Reality
10. [Unsafe Rust for C++ Veterans](book/10-unsafe-rust-for-cpp-veterans.md)
11. [Concurrency Without Data Races](book/11-concurrency-without-data-races.md)
12. [When Rust Feels Hard—and Why](book/12-when-rust-feels-hard.md)

### Appendices
- [Appendix A: Common C++ Pitfalls and Their Rust Counterparts](book/appendices/appendix-a-common-cpp-pitfalls.md)
- [Appendix B: Reading Rust Compiler Errors Like a Human](book/appendices/appendix-b-reading-rust-compiler-errors.md)
- [Appendix C: Mental Model Cheat Sheet (C++ → Rust)](book/appendices/appendix-c-mental-model-cheat-sheet.md)

### Back Matter
- [Acknowledgments](book/99-acknowledgments.md)
- [About the Author](book/99-about-the-author.md)
- [Further Reading](book/99-further-reading.md)

## Status

**Version:** 1.0.0  
**Release Date:** January 2026  
**Rust Edition:** 2021 (stable)

All code examples compile on stable Rust as of January 2026.

## License

**Book Text:** [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)  
**Code Examples:** [MIT License](LICENSE)

See [LICENSE](LICENSE) for full details.

## Contributing

Found a typo or technical error? Open an issue or submit a pull request.

Please maintain the book's voice and scope:
- Target audience is experienced C++ developers
- Focus on mental model translation, not comprehensive coverage
- Maintain calm, non-evangelical tone
- No marketing language or overpromising

## Author

Robin George Koshy - Systems programmer with 12+ years of C++ experience in automotive industry.

See [About the Author](book/99-about-the-author.md) for more context.

## Reading Online

The book is written in Markdown and readable directly on GitHub. Start with the [Preface](book/00-preface.md) or jump to the [Table of Contents](book/README.md).
