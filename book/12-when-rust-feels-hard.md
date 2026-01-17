# 12. When Rust Feels Hard—and Why

> You've learned the rules, fought the borrow checker, and written code that compiles. But it still feels harder than C++. That's not a failure—it's the cost of the guarantees Rust provides.

---

## Why This Matters to a C++ Developer

In C++, you can prototype quickly. You write code, it compiles, and you iterate. If there's a bug, you find it later—in testing, in code review, or in production.

Rust doesn't work that way. You fight the compiler upfront. You restructure your code to satisfy the borrow checker. You add lifetime annotations. You clone data when you'd prefer to borrow. The iteration loop is slower.

This feels like friction. You know what you want to do. You know it would work in C++. But Rust says no, and you have to figure out why.

The question is: is this friction worth it?

The answer depends on:
- The size of your team
- The lifespan of your codebase
- The cost of bugs in production
- Your tolerance for upfront complexity vs deferred debugging

Rust trades fast prototyping for fewer bugs. C++ trades compiler flexibility for runtime vigilance. Neither is universally better—they optimize for different constraints.

---

## The C++ Mental Model

In C++, you optimize for **iteration speed**.

**The compiler is permissive.**  
It lets you write code that might be wrong. You find out later whether it was correct. This is fast when you're exploring, slow when you're debugging.

**Mistakes are deferred.**  
You write code, it compiles, and you test it. If there's a bug, you fix it. If the bug is subtle (data race, use-after-free), you might not find it until production.

**Refactoring is risky.**  
You change a function signature, and the code still compiles. But now some callers have dangling pointers, or race conditions, or memory leaks. The compiler doesn't tell you.

**Discipline scales poorly.**  
In a small team, everyone knows the rules. In a large team, with turnover and deadlines, discipline breaks down. Bugs slip through.

**Tooling fills the gaps.**  
You use sanitizers, static analyzers, and code review to catch bugs. But these are after-the-fact. They don't prevent bugs—they find them.

This model works when:
- You're prototyping and need to move fast
- The team is small and experienced
- The cost of bugs is low (you can patch quickly)

---

## Where the Model Breaks Down

The problem is that **the cost of bugs grows with scale**.

**Bugs are expensive.**  
A use-after-free in production can crash your service, corrupt data, or create a security vulnerability. The cost of finding and fixing it is high—much higher than preventing it upfront.

**Refactoring is fragile.**  
You change a function to take ownership instead of borrowing. The code compiles. But now some callers have dangling pointers. You don't find out until production.

**Discipline doesn't scale.**  
In a large codebase, with multiple teams and years of history, conventions drift. Someone forgets to lock a mutex. Someone stores a raw pointer that outlives the object. The compiler doesn't notice.

**Tooling is incomplete.**  
Sanitizers catch many bugs, but not all. They don't catch logic errors. They don't catch race conditions that only happen under load. They only help if you run the code path that triggers the bug.

**The cost is deferred.**  
You write the code, it compiles, and you find out later whether it was correct. By then, the bug is in production, and you're debugging a crash with no clear cause.

Rust inverts this. You pay the cost upfront, at compile time. The compiler is strict. It rejects code that might be wrong. You fight the borrow checker, you add lifetime annotations, you restructure your code.

This is slower at first. But once the code compiles, whole classes of bugs are gone:
- No use-after-free
- No dangling pointers
- No data races
- No iterator invalidation

The trade-off is explicit:
- Slower prototyping, fewer bugs
- More upfront complexity, less deferred debugging
- Stricter compiler, safer code

Whether this is worth it depends on your project. If you're writing a prototype that will be thrown away, C++ is faster. If you're writing a service that will run for years, with multiple teams, Rust's guarantees pay off.

The friction you feel isn't Rust being difficult for no reason. It's Rust enforcing guarantees that C++ leaves to you. The question is whether those guarantees are worth the cost.

---

