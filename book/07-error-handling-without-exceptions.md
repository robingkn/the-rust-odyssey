# 7. Error Handling Without Exceptions

> In C++, you throw exceptions for errors and catch them somewhere up the stack. Rust doesn't have exceptions—errors are values, and you handle them explicitly at every step.

---

## Why This Matters to a C++ Developer

You use exceptions for error handling. When something goes wrong, you throw. The stack unwinds, destructors run, and control jumps to the nearest catch block. It's automatic, it's clean, and it separates error handling from normal control flow.

You structure your code around this:
- Constructors throw if initialization fails
- Functions throw if they can't complete
- Catch blocks handle errors at the appropriate level
- RAII ensures cleanup happens during unwinding

This works well for many cases:
- Errors propagate automatically
- You don't clutter normal code with error checks
- Destructors guarantee cleanup

But it has costs:
- Exception safety is hard to reason about
- Performance is unpredictable (zero-cost until you throw)
- You can't tell from a function signature whether it throws
- Exceptions across library boundaries are fragile

In practice, many C++ codebases avoid exceptions entirely. Google's style guide bans them. Game engines disable them. Embedded systems can't afford them. You use error codes, `std::optional`, or `std::expected` instead.

---

## The C++ Mental Model

In C++, exceptions are **invisible control flow**.

**Errors propagate automatically.**  
You throw an exception, and it travels up the stack until someone catches it. You don't have to manually check and forward errors at every level.

```cpp
void process() {
    auto file = open_file("data.txt");  // Throws if file doesn't exist
    // No error checking needed here
}
```

**Error handling is separate from logic.**  
Your normal code path is clean. Error handling happens in catch blocks, away from the main logic.

```cpp
try {
    step1();
    step2();
    step3();
} catch (const std::exception& e) {
    // Handle all errors here
}
```

**Exceptions are expensive (when thrown).**  
The happy path is fast—no overhead. But when you throw, the cost is high: stack unwinding, destructor calls, and searching for catch blocks. This is fine if exceptions are rare.

**Exception safety is a discipline.**  
You must ensure your code is exception-safe: basic guarantee (no leaks), strong guarantee (rollback on failure), or nothrow guarantee. The compiler doesn't enforce this—you do.

**Exceptions are invisible in signatures.**  
A function signature doesn't tell you what exceptions it might throw (unless you use `noexcept`). You have to read the documentation or the implementation.

```cpp
void process();  // Does this throw? What exceptions? Unknown.
```

This model works when:
- Errors are exceptional (rare)
- You can afford the runtime cost
- You trust all code in the call stack to be exception-safe

---

## Where the Model Breaks Down

The problem is that **exceptions are invisible until they're not**.

**You can't tell what might fail.**  
A function call might throw, or it might not. The compiler doesn't tell you. You have to know, or you have to assume everything throws.

```cpp
void process(const std::string& data) {
    auto result = parse(data);  // Does this throw? Maybe.
    save(result);               // Does this throw? Probably.
}
```

**Error handling is deferred.**  
You don't have to handle errors where they occur. You can let them propagate. This is convenient, but it means errors can surface far from their source.

**Exception safety is hard.**  
You must ensure that every operation either completes or leaves the program in a valid state. This is difficult when exceptions can be thrown from anywhere.

```cpp
void transfer(Account& from, Account& to, int amount) {
    from.withdraw(amount);  // What if this throws after modifying state?
    to.deposit(amount);     // What if this throws?
}
```

**Performance is unpredictable.**  
The happy path is fast, but the error path is slow. If errors are common (parsing user input, network failures), exceptions become a performance problem.

**Exceptions don't cross boundaries well.**  
C APIs don't understand exceptions. Throwing across a C library is undefined behavior. You have to catch at the boundary and convert to error codes.

Rust doesn't have exceptions. Errors are values—`Result<T, E>`—and you handle them explicitly. Every function that can fail returns a `Result`. You can't ignore it. You can't let it propagate without acknowledging it.

This is more verbose. You check errors at every step. But it's also more explicit: you know exactly what can fail, and you decide how to handle it. The compiler ensures you don't ignore errors.

---

