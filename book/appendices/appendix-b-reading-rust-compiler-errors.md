# Appendix B. Reading Rust Compiler Errors Like a Human

> C++ template errors span hundreds of lines and point deep into the standard library. Rust errors are verbose but structured—once you learn to read them, they're actually helpful.

---

## Why This Matters to a C++ Developer

You're used to compiler errors that are cryptic, verbose, and often misleading. Template errors show you what went wrong deep in the implementation, not what you did wrong at the call site.

In C++, you handle errors by:
- Scrolling through pages of template instantiation traces
- Guessing what the compiler actually wants
- Commenting out code until it compiles
- Searching Stack Overflow for the error message
- Learning to recognize patterns in the noise

Rust errors are different. They're verbose, but they're structured. They tell you:
- What you tried to do
- Why it's not allowed
- Where the conflict is
- How to fix it (often with suggestions)

The first week, they'll feel overwhelming. After that, they become a teaching tool.

---

## The C++ Mental Model

In C++, compiler errors are **diagnostic noise**.

**Template errors are incomprehensible.**  
A simple mistake generates hundreds of lines of errors, deep in the standard library.

```cpp
template<typename T>
void process(T& container) {
    std::sort(container.begin(), container.end());
}

std::list<int> lst = {3, 1, 2};
process(lst);  // Error: no match for 'operator-' in '__last - __first'
               // (followed by 50+ lines of template instantiation)
```

The error doesn't say "std::list doesn't have random-access iterators." It shows you where `std::sort` failed, deep in the implementation.

**Errors point to the wrong place.**  
The error appears where the template is instantiated, not where you made the mistake.

**No suggestions.**  
The compiler tells you what's wrong, but not how to fix it. You have to figure that out.

**SFINAE errors are silent.**  
If a template substitution fails, the compiler just tries the next overload. You don't know why the first one didn't match.

---

## Where the Model Breaks Down

**Errors are overwhelming.**  
A single mistake generates pages of output. You have to learn to ignore most of it and find the relevant line.

**Errors are misleading.**  
The compiler shows you where the code failed, not why. You have to work backwards from the error to the cause.

**No learning feedback.**  
The compiler doesn't teach you. It just tells you "no" and expects you to figure out why.

**Concept errors (C++20) help, but aren't universal.**  
Concepts improve error messages, but most codebases don't use them yet.

---

## Rust's Model

Rust errors are structured and informative. They tell you what's wrong, where, and often how to fix it.

### Error Anatomy

A typical Rust error has:
1. **Error code** (e.g., `E0382`)
2. **Error message** (what you did wrong)
3. **Location** (file, line, column)
4. **Code snippet** with annotations
5. **Explanation** (why it's wrong)
6. **Suggestion** (how to fix it)

### Example 1: Use After Move

```rust
fn main() {
    let s = String::from("hello");
    let s2 = s;
    println!("{}", s);
}
```

**Error:**
```
error[E0382]: borrow of moved value: `s`
 --> src/main.rs:4:20
  |
2 |     let s = String::from("hello");
  |         - move occurs because `s` has type `String`, which does not implement the `Copy` trait
3 |     let s2 = s;
  |              - value moved here
4 |     println!("{}", s);
  |                    ^ value borrowed here after move
  |
  = note: this error originates in the macro `$crate::format_args_nl` which comes from the expansion of the macro `println` (in Nightly builds, run with -Z macro-backtrace for more info)
help: consider cloning the value if the performance cost is acceptable
  |
3 |     let s2 = s.clone();
  |               ++++++++
```

(The note about macro expansion can be ignored - it's just showing that println! is implemented as a macro. The key error is above.)

**What it tells you:**
- You moved `s` on line 3
- You tried to use it on line 4
- `String` doesn't implement `Copy`
- Suggestion: use `.clone()` if you want a copy

### Example 2: Borrow Checker Violation

```rust
fn main() {
    let mut vec = vec![1, 2, 3];
    let first = &vec[0];
    vec.push(4);
    println!("{}", first);
}
```

**Error:**
```
error[E0502]: cannot borrow `vec` as mutable because it is also borrowed as immutable
 --> src/main.rs:4:5
  |
3 |     let first = &vec[0];
  |                  --- immutable borrow occurs here
4 |     vec.push(4);
  |     ^^^^^^^^^^^ mutable borrow occurs here
5 |     println!("{}", first);
  |                    ----- immutable borrow later used here
```

**What it tells you:**
- You borrowed `vec` immutably on line 3
- You tried to borrow it mutably on line 4
- The immutable borrow is still in use on line 5
- You can't have both at the same time

### Example 3: Lifetime Error

```rust
fn longest(s1: &str, s2: &str) -> &str {
    if s1.len() > s2.len() { s1 } else { s2 }
}
```

**Error:**
```
error[E0106]: missing lifetime specifier
 --> src/main.rs:1:38
  |
1 | fn longest(s1: &str, s2: &str) -> &str {
  |                ----      ----     ^ expected named lifetime parameter
  |
  = help: this function's return type contains a borrowed value, but the signature does not say whether it is borrowed from `s1` or `s2`
help: consider introducing a named lifetime parameter
  |
1 | fn longest<'a>(s1: &'a str, s2: &'a str) -> &'a str {
  |           ++++      ++           ++          ++
```

**What it tells you:**
- The return type is a reference
- The compiler doesn't know which input it borrows from
- Suggestion: add lifetime annotations

### Example 4: Type Mismatch

```rust
fn main() {
    let x: i32 = "hello";
}
```

**Error:**
```
error[E0308]: mismatched types
 --> src/main.rs:2:18
  |
2 |     let x: i32 = "hello";
  |            ---   ^^^^^^^ expected `i32`, found `&str`
  |            |
  |            expected due to this
```

**What it tells you:**
- You declared `x` as `i32`
- You assigned a `&str`
- Clear type mismatch

### Reading Strategies

**1. Start with the error code.**  
`E0382`, `E0502`, etc. You can look these up: `rustc --explain E0382`

**2. Read the first line.**  
It tells you what you did wrong in plain English.

**3. Look at the annotations.**  
The `^`, `-`, and `|` markers show you exactly where the problem is.

**4. Read the help text.**  
Often includes a suggestion or explanation.

**5. Ignore macro expansion notes (at first).**  
They're usually not relevant to your mistake.

**6. Fix one error at a time.**  
Later errors often disappear when you fix the first one.

---

## Takeaways for C++ Developers

**Mental model shift:**
- Rust errors are teaching tools, not just diagnostics
- The compiler is trying to help, not just reject your code
- Error codes (`E0382`) are documentation references
- Suggestions are often correct—try them

**Reading strategy:**
1. Read the first line (what you did wrong)
2. Look at the code annotations (where the conflict is)
3. Read the help text (how to fix it)
4. Try the suggestion if provided
5. Look up the error code if confused: `rustc --explain E0382`

**Common error patterns:**
- `E0382`: Use after move → You moved a value and tried to use it
- `E0502`: Borrow conflict → You tried to borrow mutably while borrowed immutably
- `E0106`: Missing lifetime → The compiler can't infer the lifetime
- `E0308`: Type mismatch → You used the wrong type
- `E0499`: Multiple mutable borrows → You tried to borrow mutably twice

**Pitfalls:**
- Don't ignore the error and try random fixes—read it
- Don't fight the compiler—it's preventing a bug
- Suggestions aren't always optimal—but they're a starting point
- Multiple errors often have one root cause—fix the first one

Rust errors are verbose, but they're structured. Once you learn to read them, they're more helpful than C++ errors.

---
