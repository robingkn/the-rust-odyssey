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
    return &w;  // Dangling pointer. Modern compilers warn or error on this.
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


## Rust's Model

Rust makes ownership a first-class language feature. Every value has exactly one owner, and the compiler enforces this.

**Ownership is explicit in the type system.**  
When you pass a value to a function, you're either transferring ownership or borrowing. The compiler tracks this.

```rust
fn process(data: Vec<i32>) {
    // Takes ownership of data
    println!("Processing {} items", data.len());
}  // data is dropped here

fn main() {
    let vec = vec![1, 2, 3];
    process(vec);
    // println!("{:?}", vec);  // Compile error: value moved
}
```

Compare to C++:
```cpp
void process(std::vector<int> data);  // Copy? Move? Unclear.
```

In Rust, the signature tells you: `process` takes ownership. After the call, `vec` is gone.

**Transfer is automatic and checked.**  
Move semantics are the default. The compiler prevents use-after-move.

```rust
let s1 = String::from("hello");
let s2 = s1;  // s1 moved to s2
// println!("{}", s1);  // Compile error: value moved
```

In C++, you'd use `std::move(s1)`, but nothing stops you from using `s1` afterward. In Rust, the compiler prevents it.

**Borrowing is explicit and tracked.**  
When you don't want to transfer ownership, you borrow with `&` or `&mut`.

```rust
fn read_data(data: &Vec<i32>) {
    // Borrows data, doesn't take ownership
    println!("First item: {}", data[0]);
}  // Borrow ends here

fn main() {
    let vec = vec![1, 2, 3];
    read_data(&vec);
    println!("{:?}", vec);  // Still valid
}
```

The compiler ensures the borrow doesn't outlive the owner. You can't return a reference to local data:

```rust
// This won't compile:
// fn get_data() -> &Vec<i32> {
//     let vec = vec![1, 2, 3];
//     &vec  // Error: vec doesn't live long enough
// }
```

**Shared ownership is explicit and opt-in.**  
If you need multiple owners, you use `Rc<T>` (reference-counted) or `Arc<T>` (atomic reference-counted). The cost is visible in the type.

```rust
use std::rc::Rc;

fn main() {
    let data = Rc::new(vec![1, 2, 3]);
    let data2 = Rc::clone(&data);  // Increment reference count
                                    // (Preferred over data.clone() for clarity)
    
    println!("{:?}", data);
    println!("{:?}", data2);
}  // Both dropped, reference count reaches 0, data freed
```

Unlike C++ `shared_ptr`, you can't accidentally create shared ownership. You have to explicitly use `Rc` or `Arc`.

---

## Takeaways for C++ Developers

**Mental model shift:**
- Ownership is not a convention—it's part of the type system
- Move is the default, not opt-in like `std::move`
- Borrowing is explicit (`&T`), and the compiler tracks lifetimes
- Shared ownership requires explicit types (`Rc`, `Arc`)

**Rules of thumb:**
- Pass by value (`T`) when transferring ownership
- Pass by reference (`&T`) when borrowing
- Use `&mut T` when you need to modify without taking ownership
- Use `Rc`/`Arc` only when you truly need shared ownership

**Pitfalls:**
- Don't fight the ownership system—restructure your code instead
- Cloning (`data.clone()`) is sometimes the right answer, not a code smell
- `Rc` is not thread-safe—use `Arc` for concurrent access
- The compiler's error messages about "moved value" mean you tried to use something after transferring ownership

Rust's ownership system is what C++ smart pointers try to be, but enforced by the compiler instead of discipline.

---
