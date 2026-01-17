# 8. Traits vs Templates

> In C++, templates are duck-typed—if it compiles, it works. Rust traits are explicit contracts, and suddenly you're declaring interfaces for generic code that would "just work" in C++.

---

## Why This Matters to a C++ Developer

You use templates for generic code. You write a function that works with any type that supports the operations you need. The compiler checks this when you instantiate the template—not when you define it.

```cpp
template<typename T>
T add(T a, T b) {
    return a + b;  // Works if T has operator+
}
```

This is powerful:
- No explicit interface declarations
- Works with any type that fits
- Compiler generates specialized code for each instantiation

You don't declare "this type must support addition"—you just use addition, and the compiler figures it out. If the type doesn't support it, you get a compile error at the call site.

This works through:
- Duck typing ("if it quacks like a duck...")
- SFINAE for conditional compilation
- Concepts (C++20) for explicit constraints (if you use them)

The cost is:
- Error messages that span hundreds of lines
- No way to see what operations a template requires
- Accidental interface coupling

---

## The C++ Mental Model

In C++, templates are **structural**—they care about what you can do, not what you claim to be.

**Interfaces are implicit.**  
A template doesn't declare what operations it needs. It just uses them. If the type supports those operations, it works. If not, you get a compile error.

```cpp
template<typename T>
void process(T& container) {
    container.push_back(42);  // Requires push_back method
}
```

**Constraints are optional.**  
You can use SFINAE or concepts to constrain templates, but most code doesn't. The template just tries to compile, and fails if it can't.

**Instantiation is late.**  
The compiler checks the template when you use it, not when you define it. This means errors appear at the call site, not at the definition.

```cpp
template<typename T>
T multiply(T a, T b) {
    return a * b;
}

// Error appears here, not in the template definition
multiply(std::string("hello"), std::string("world"));
```

**Specialization is powerful.**  
You can specialize templates for specific types, providing completely different implementations. The compiler picks the best match.

```cpp
template<typename T>
void print(T value) { std::cout << value; }

template<>
void print<bool>(bool value) { std::cout << (value ? "true" : "false"); }
```

This model is flexible:
- You don't have to declare interfaces upfront
- Templates work with any type that fits
- You can specialize for specific cases

---

## Where the Model Breaks Down

The problem is that **templates hide their requirements**.

**Error messages are incomprehensible.**  
When a template fails to compile, you get errors deep in the implementation, not at the interface. The error message shows you what went wrong, but not what the template actually requires.

```cpp
template<typename T>
void process(T& container) {
    auto it = container.begin();
    std::sort(it, container.end());
}

// Error: no matching function for call to 'sort'
// (because std::list doesn't have random-access iterators)
// But the error doesn't say "T must have random-access iterators"
```

**Interfaces are accidental.**  
A template's interface is whatever operations it happens to use. If you refactor the implementation, the interface changes. Callers break, even though you didn't intend to change the interface.

**Constraints are hard to express.**  
SFINAE is arcane. Concepts (C++20) help, but they're verbose and not widely used yet. Most templates just try to compile and fail with cryptic errors.

**Instantiation bloat.**  
Every instantiation generates new code. If you instantiate a template with 10 types, you get 10 copies of the code. This increases binary size and compile time.

**No separate compilation.**  
Templates must be defined in headers. You can't compile them separately. This slows down builds and exposes implementation details.

Rust uses traits—explicit interfaces for generic code. A trait declares what operations a type must support. A generic function declares which traits it requires. The compiler checks this at the definition, not at the call site.

This is more verbose. You have to declare traits, implement them for your types, and specify trait bounds on generic functions. But it's also more explicit: you know exactly what a generic function requires, and errors point to the interface, not the implementation.

---

