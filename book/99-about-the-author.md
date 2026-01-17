## About the Author

Robin George Koshy is a Senior Software Engineer with **12+ years of experience** building systems-level C++ software in the automotive domain. His career includes work at Bosch and MathWorks, where he focused on production systems where correctness, performance, and long-term maintainability matter more than elegance on a whiteboard.

His work spans core C++ infrastructure, real-time and concurrent systems, signal processing pipelines, and applied AI/ML—often at the intersection of hardware, data, and safety-critical software. Like many C++ engineers in automotive, his day-to-day work involved explicit memory management, complex ownership conventions, defensive coding patterns, and debugging failures that only surfaced in production.

Learning Rust required unlearning assumptions that had been reinforced over years of “working” C++ code. The borrow checker rejected patterns that were familiar, idiomatic, and widely accepted—yet often fragile. The frustration was real, but so was the realization that many of those patterns relied on discipline rather than guarantees.

This book documents that learning process—not as a conversion story, but as a translation guide. Rust and C++ solve similar systems problems under different constraints and with different trade-offs. Understanding Rust requires mapping existing C++ mental models to a language that encodes invariants directly into the type system.

The goal was to write the book that didn’t exist when learning Rust: one that assumes deep systems programming experience and focuses on *why* Rust works the way it does, not just *how* to use it.

If you’ve spent years writing C++ and are seriously evaluating Rust, this book is that map.
