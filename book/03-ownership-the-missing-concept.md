# 3. Ownership: The Missing Concept in C++

> In C++, you know who owns a resource—it's in the variable name, the comment, or the convention. Rust makes ownership a first-class language feature, and suddenly every pointer decision becomes explicit.

---

## Why This Matters to a C++ Developer

You already think about ownership. Every time you choose between `unique_ptr`, `shared_ptr`, or a raw pointer, you're making an ownership decision.

- `unique_ptr<T>`: I own this, and I'm the only owner
- `shared_ptr<T>`: Ownership is shared, reference-counted
- `T*`: I don't own this, someone else does (probably)

This works through discipline:
- Naming conventions (`owner_`, `borrowed_`)
- Code review ("Who deletes this?")
- Documentation ("Caller retains ownership")
- Smart pointers that encode intent

The compiler doesn't enforce any of this. It trusts you to get it right. When you don't, you get:
- Double frees
- Use-after-free
- Memory leaks
- Dangling pointers

These bugs are rare in well-written C++, but they're catastrophic when they happen. And they're invisible to the compiler.

---

## The C++ Mental Model

In C++, ownership is a **convention**, not a rule.

**Ownership is implicit.**  
You pass a pointer to a function. Does the function take ownership? Maybe. It depends on the documentation, the function name, or the team's coding standards. The compiler doesn't know and doesn't care.

```cpp
void process(Widget* w);  // Who owns w? Unclear.
```

**Transfer is manual.**  
When you want to transfer ownership, you use `std::move` or pass a `unique_ptr`. But nothing stops you from using the object after the move—it's just undefined behavior.

```cpp
auto ptr = std::make_unique<Widget>();
consume(std::move(ptr));
ptr->do_something();  // Compiles. Undefined behavior.
```

**Shared ownership is opt-in.**  
If you need multiple owners, you use `shared_ptr`. It's explicit, reference-counted, and has runtime overhead. Most of the time, you avoid it.

**Borrowing is invisible.**  
When you pass a reference or a raw pointer, you're borrowing—but the compiler doesn't track it. If the owner destroys the object while you're still using it, that's your problem.

```cpp
Widget* get_widget() {
    Widget w;
    return &w;  // Dangling pointer. Compiler might warn, but won't stop you.
}
```

This model works because:
- Experienced developers internalize the rules
- Code review catches mistakes
- Smart pointers make ownership explicit (when you use them)

---

## Where the Model Breaks Down

The problem is that **ownership violations look like normal code**.

**The compiler can't help.**  
It doesn't know which pointers are owners and which are borrowers. It can't tell you when a borrow outlives the owner. It can't prevent you from using a moved-from object.

**Conventions don't scale.**  
In a small codebase, everyone knows the rules. In a large codebase, with multiple teams and years of history, conventions drift. Someone passes a raw pointer where a `unique_ptr` was expected. Someone stores a reference that outlives the object.

**Refactoring is fragile.**  
You change a function to take ownership instead of borrowing. Every caller still compiles—but now some of them have dangling pointers. The compiler doesn't notice. Your tests might not catch it. Production does.

**The cost is deferred.**  
You write the code, it compiles, and you find out later whether the ownership was correct. By then, the bug is in production, and you're debugging a crash that only happens under load.

Rust doesn't let you defer this. Ownership is explicit, checked at compile time, and non-negotiable. Every value has exactly one owner. When the owner goes out of scope, the value is destroyed. If you want to borrow, the compiler tracks the lifetime and ensures the borrow doesn't outlive the owner.

This feels restrictive at first—because it is. But it eliminates an entire class of bugs that C++ leaves to you.

---

