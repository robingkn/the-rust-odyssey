# 6. RAII Reimagined

> You already know RAII—constructors acquire, destructors release. Rust uses the same pattern, but ties it to ownership in a way that makes resource management both more predictable and more restrictive.

---

## Why This Matters to a C++ Developer

RAII is one of C++'s best ideas. You acquire a resource in a constructor, release it in a destructor, and the compiler handles cleanup automatically. No manual `free()`, no forgetting to close a file, no leaking mutexes.

You use it everywhere:
- `std::unique_ptr` for memory
- `std::lock_guard` for mutexes
- `std::fstream` for files
- Custom RAII wrappers for anything else

This works through discipline:
- Always use RAII types for resources
- Never call `new` without a smart pointer
- Never acquire a resource without a corresponding RAII wrapper

When you follow the rules, resource leaks are rare. When you don't, you get:
- Memory leaks
- File descriptor exhaustion
- Deadlocks from unreleased locks
- Undefined behavior from double-free

The compiler doesn't enforce RAII. It's a pattern, not a language feature. You choose to use it—or you don't.

---

## The C++ Mental Model

In C++, RAII is a **convention** built on destructors.

**Destructors are guaranteed.**  
When an object goes out of scope, its destructor runs. This is deterministic, predictable, and happens in reverse order of construction.

```cpp
{
    std::lock_guard<std::mutex> lock(mtx);
    // Critical section
}  // lock released here, automatically
```

**RAII is opt-in.**  
You can use raw pointers, manual `delete`, and explicit resource management if you want. The language doesn't force you to use RAII—it just makes it convenient.

```cpp
int* ptr = new int(42);  // No RAII. You must delete manually.
delete ptr;
```

**Ownership is implicit.**  
A RAII type owns the resource, but the compiler doesn't track this. You can copy a `unique_ptr` (if you disable the copy constructor), move from it and then use it, or store a raw pointer to the managed resource.

```cpp
auto ptr = std::make_unique<int>(42);
int* raw = ptr.get();
ptr.reset();  // Resource destroyed
*raw = 10;    // Dangling pointer. Undefined behavior.
```

**Destructors can fail.**  
A destructor can throw an exception (though it shouldn't). If it does during stack unwinding, your program terminates. You handle this by making destructors `noexcept` and logging errors instead of throwing.

This model works because:
- RAII types are well-understood
- Smart pointers are standard library features
- Destructors are deterministic and predictable

---

## Where the Model Breaks Down

The problem is that **RAII doesn't prevent misuse**.

**You can bypass RAII.**  
Nothing stops you from calling `new` and forgetting to `delete`. Nothing stops you from storing a raw pointer to a RAII-managed resource and using it after the resource is destroyed.

```cpp
std::unique_ptr<int> ptr = std::make_unique<int>(42);
int* raw = ptr.get();
ptr.reset();
*raw = 10;  // Compiles. Undefined behavior.
```

**Move semantics are unchecked.**  
You can move from a RAII object and then use it. The compiler doesn't stop you. The object is in a valid-but-unspecified state, and using it is your problem.

```cpp
auto ptr = std::make_unique<int>(42);
auto ptr2 = std::move(ptr);
*ptr = 10;  // Undefined behavior. Compiles.
```

**Destructors are invisible.**  
You don't see when a destructor runs—it just happens. This is usually good, but it can be surprising. A temporary object is destroyed at the end of the statement, not the end of the scope.

```cpp
std::lock_guard<std::mutex>(mtx);  // Lock acquired and immediately released!
// Critical section is NOT protected
```

**Copying RAII types is fragile.**  
Some RAII types are copyable (`shared_ptr`), some are move-only (`unique_ptr`), and some are neither (`lock_guard`). The rules are inconsistent, and the compiler doesn't help you understand why.

Rust ties RAII to ownership. Every value has exactly one owner, and when the owner goes out of scope, the destructor runs. You can't bypass this. You can't use a value after it's been moved. You can't copy a RAII type unless it explicitly implements `Clone`.

This makes RAII more restrictive—but also more predictable. You don't have to remember to use smart pointers. You don't have to worry about use-after-move. The compiler enforces the pattern, not your discipline.

---

