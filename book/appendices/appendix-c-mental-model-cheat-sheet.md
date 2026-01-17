# Appendix C. Mental Model Cheat Sheet (C++ → Rust)

> You think in C++ patterns—pointers, references, RAII, templates. Here's how they map to Rust, and where they don't.

---

## Why This Matters to a C++ Developer

You have 15+ years of C++ intuition. You know when to use `unique_ptr`, when to pass by reference, when to use `const`. These patterns are muscle memory.

Rust has similar concepts, but different rules. Your C++ intuition will get you 80% of the way there—but the remaining 20% is where you'll fight the compiler.

This cheat sheet maps C++ patterns to Rust equivalents:
- What translates directly
- What requires restructuring
- What doesn't exist in Rust
- What Rust has that C++ doesn't

Use this as a reference when you're stuck, or when a C++ pattern doesn't work in Rust.

---

## The C++ Mental Model

In C++, you think in terms of:
- Ownership (implicit, by convention)
- References (aliases, unchecked)
- Smart pointers (opt-in RAII)
- Templates (duck-typed, checked at instantiation)
- Exceptions (invisible control flow)
- Mutability (default, const is opt-in)

These patterns work through discipline and conventions. The compiler trusts you to get it right.

---

## Where the Model Breaks Down

C++ patterns don't always map cleanly to Rust:
- Ownership is explicit, not implicit
- References have lifetimes that the compiler tracks
- RAII is mandatory, not opt-in
- Generics use traits, not duck typing
- Errors are values, not exceptions
- Mutability is opt-in, not default

You'll need to adjust your mental model. This cheat sheet shows you how.

---

## Rust's Model

### Ownership and Memory Management

| C++ Pattern | Rust Equivalent | Notes |
|-------------|-----------------|-------|
| `T*` (raw pointer) | `*const T` or `*mut T` | Requires `unsafe` to dereference |
| `T&` (reference) | `&T` | Immutable by default, lifetime-tracked |
| `T&` (mutable) | `&mut T` | Exclusive mutable access |
| `const T&` | `&T` | Immutable reference |
| `std::unique_ptr<T>` | `Box<T>` | Heap allocation, single owner |
| `std::shared_ptr<T>` | `Rc<T>` | Reference-counted (single-threaded) |
| `std::shared_ptr<T>` (thread-safe) | `Arc<T>` | Atomic reference-counted |
| `new T` | `Box::new(T)` | Heap allocation |
| `delete ptr` | `drop(ptr)` | Usually automatic |
| `std::move(x)` | `x` (move is default) | Move is implicit in Rust |

**Key differences:**
- Rust moves by default; C++ copies by default
- Rust tracks lifetimes; C++ doesn't
- Rust prevents use-after-move; C++ allows it (UB)

### References and Borrowing

| C++ Pattern | Rust Equivalent | Notes |
|-------------|-----------------|-------|
| `void f(T& x)` | `fn f(x: &mut T)` | Mutable reference |
| `void f(const T& x)` | `fn f(x: &T)` | Immutable reference |
| Multiple `const T&` | Multiple `&T` | Allowed |
| Multiple `T&` | One `&mut T` | Only one mutable borrow at a time |
| Return reference to local | Compile error | Rust prevents this |
| Dangling pointer | Compile error | Rust prevents this |

**Key differences:**
- Rust enforces aliasing XOR mutability
- Rust tracks reference lifetimes
- Rust prevents dangling references at compile time

### Types and Generics

| C++ Pattern | Rust Equivalent | Notes |
|-------------|-----------------|-------|
| `template<typename T>` | `fn f<T>()` | Generic function |
| `template<typename T> class C` | `struct S<T>` | Generic struct |
| SFINAE | Trait bounds | `fn f<T: Trait>()` |
| Concepts (C++20) | Trait bounds | More explicit in Rust |
| Virtual functions | Trait objects | `&dyn Trait` or `Box<dyn Trait>` |
| Abstract base class | Trait | Define interface |
| Multiple inheritance | Multiple traits | Traits can be composed |
| `static_cast<T>` | `as` or `From`/`Into` | Type conversion |
| `dynamic_cast<T>` | `downcast` (limited) | Less common in Rust |

**Key differences:**
- Rust traits are explicit; C++ templates are duck-typed
- Rust checks trait bounds at definition; C++ checks at instantiation
- Rust trait objects are explicit (`dyn`); C++ virtual is implicit

### Error Handling

| C++ Pattern | Rust Equivalent | Notes |
|-------------|-----------------|-------|
| `throw Exception` | `return Err(e)` | Errors are values |
| `try { } catch { }` | `match result { Ok/Err }` | Pattern matching |
| Exception propagation | `?` operator | Explicit propagation |
| `noexcept` | No equivalent | Rust has no exceptions |
| `std::optional<T>` | `Option<T>` | Explicit null handling |
| `nullptr` | `None` | Part of `Option<T>` |
| Null pointer check | `if let Some(x)` | Compiler enforces |

**Key differences:**
- Rust has no exceptions—use `Result<T, E>`
- Errors are explicit in function signatures
- `Option<T>` makes null explicit

### Mutability and Const

| C++ Pattern | Rust Equivalent | Notes |
|-------------|-----------------|-------|
| `T x` (mutable) | `let mut x: T` | Mutable variable |
| `const T x` | `let x: T` | Immutable by default |
| `const T*` | `*const T` | Pointer to const (unsafe) |
| `T* const` | N/A | Rust pointers don't have this |
| `const T&` | `&T` | Immutable reference |
| `mutable` keyword | `Cell<T>` or `RefCell<T>` | Interior mutability |

**Key differences:**
- Rust is immutable by default; C++ is mutable by default
- Rust `mut` applies to bindings, not types
- Interior mutability is explicit in Rust

### Concurrency

| C++ Pattern | Rust Equivalent | Notes |
|-------------|-----------------|-------|
| `std::thread` | `std::thread` | Similar API |
| `std::mutex<T>` | `Mutex<T>` | Mutex owns the data |
| `std::lock_guard` | `MutexGuard` | RAII lock guard |
| `std::shared_ptr` + mutex | `Arc<Mutex<T>>` | Thread-safe shared state |
| `std::atomic<T>` | `AtomicT` | Lock-free atomics |
| Thread-local storage | `thread_local!` | Similar concept |
| Data race | Compile error | Prevented by type system |

**Key differences:**
- Rust prevents data races at compile time
- `Mutex<T>` owns the data it protects
- `Send` and `Sync` traits encode thread safety

### Collections

| C++ Pattern | Rust Equivalent | Notes |
|-------------|-----------------|-------|
| `std::vector<T>` | `Vec<T>` | Dynamic array |
| `std::array<T, N>` | `[T; N]` | Fixed-size array |
| `std::string` | `String` | Owned string |
| `std::string_view` | `&str` | String slice |
| `std::map<K, V>` | `HashMap<K, V>` | Hash map |
| `std::set<T>` | `HashSet<T>` | Hash set |
| `std::unordered_map` | `HashMap` | Rust's default is unordered |
| Iterator | Iterator | Similar concept, more powerful |

**Key differences:**
- Rust iterators are zero-cost abstractions
- Rust prevents iterator invalidation
- Rust strings are UTF-8, not arbitrary bytes

### RAII and Destructors

| C++ Pattern | Rust Equivalent | Notes |
|-------------|-----------------|-------|
| Destructor `~T()` | `impl Drop for T` | Automatic cleanup |
| RAII wrapper | Any type with `Drop` | Mandatory, not opt-in |
| `std::unique_ptr` | `Box<T>` | Automatic deallocation |
| Manual `delete` | `drop(x)` | Usually automatic |
| Destructor order | Same (reverse order) | Deterministic |

**Key differences:**
- RAII is mandatory in Rust, not opt-in
- Rust prevents use-after-move
- `Drop` can't fail (no exceptions)

### Common Patterns

| C++ Pattern | Rust Equivalent | Notes |
|-------------|-----------------|-------|
| Factory function | Associated function | `T::new()` |
| Method chaining | Method chaining | Same pattern |
| Builder pattern | Builder pattern | Common in Rust |
| PIMPL idiom | `Box<T>` | Hide implementation |
| Singleton | `lazy_static!` or `OnceCell` | Thread-safe initialization |
| Callback | Closure or `Fn` trait | Similar concept |

---

## Takeaways for C++ Developers

**Direct translations:**
- `unique_ptr<T>` → `Box<T>`
- `shared_ptr<T>` → `Rc<T>` (single-threaded) or `Arc<T>` (multi-threaded)
- `const T&` → `&T`
- `T&` → `&mut T`
- `std::vector<T>` → `Vec<T>`
- `std::optional<T>` → `Option<T>`

**Requires restructuring:**
- Multiple mutable references → Use `RefCell` or restructure
- Exceptions → Use `Result<T, E>`
- Null pointers → Use `Option<T>`
- Virtual functions → Use trait objects (`&dyn Trait`)
- Templates → Use generics with trait bounds

**Doesn't exist in Rust:**
- Raw `new`/`delete` (use `Box::new` or allocators)
- Exceptions (use `Result`)
- Null pointers (use `Option`)
- Inheritance (use composition and traits)
- Implicit conversions (use `From`/`Into` traits)

**Rust has, C++ doesn't:**
- Lifetime annotations (`'a`)
- Pattern matching (`match`)
- `?` operator for error propagation
- Trait objects (`dyn Trait`)
- Compile-time data race prevention

**Mental model adjustments:**
- Think "ownership" not "pointer"
- Think "borrow" not "reference"
- Think "trait" not "interface" or "template"
- Think "Result" not "exception"
- Think "Option" not "null"

**When stuck:**
- If the borrow checker complains, you're trying to alias and mutate
- If lifetimes are confusing, draw the ownership graph
- If traits are unclear, think "explicit interface"
- If errors are verbose, read the first line and the suggestion
- If cloning seems wrong, it might be right—explicit cost is better than hidden bugs

This cheat sheet is a starting point. The patterns will become intuitive with practice.

---
