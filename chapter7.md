# Chapter 7 — Scope, Duration, and Linkage

---

## Blocks

* A **block** (or *compound statement*) is a group of zero or more statements that the compiler treats as a single statement.
* Blocks begin and end with braces `{}`.

```cpp
{
    int x = 5;
    std::cout << x;
}
```

---

## Namespaces

### Definition

* You can create your own **namespaces** using the `namespace` keyword.
* The **global namespace** encompasses all declarations made outside any explicit namespace, block, class, or function.
* **User-defined namespaces** must appear either in the global scope or nested inside another namespace.

```cpp
namespace Foo {
    int doSomething() {
        return 1;
    }
}

int main() {
    int y = Foo::doSomething(); // Accessing namespace member
}
```

### Global Scope Resolution

* The scope resolution operator `::` can also be used **with no preceding namespace** to indicate lookup in the **global namespace**:

```cpp
::foo(); // Calls global version of foo()
```

### Namespaces Across Files

* Namespaces can be distributed across multiple files:

```cpp
// Foo.hpp
namespace Foo {
    int doSomething();
}

// Foo2.hpp
namespace Foo {
    int doSomething2();
}
```

* The standard library uses this pattern extensively — each header (like `<vector>`) contributes to namespace `std`.

### Nested Namespaces

Namespaces can be nested:

```cpp
namespace Foo {
    namespace Goo {
        void bar();
    }
}
```

Since C++17, you can write this more concisely:

```cpp
namespace Foo::Goo {
    void bar();
}
```

### Namespace Aliases

You can create an alias for a namespace:

```cpp
namespace Active = Foo::Goo;
Active::doSomething();
```

This makes it easy to refactor namespace structures — you only need to update the alias, not all calls.

### Design Philosophy

* Namespaces were designed to **prevent naming collisions**, not as an organizational hierarchy.
* Use them to prevent symbol conflicts, especially in distributed or reusable code.

---

## Key Concepts Recap

| Concept      | Definition                                                                                           |
| ------------ | ---------------------------------------------------------------------------------------------------- |
| **Scope**    | Defines where an identifier is visible. All variable names within a scope must be unique.            |
| **Duration** | Defines when a variable is instantiated and destroyed.                                               |
| **Lifetime** | Defines when an identifier exists and can be used.                                               |
| **Linkage**  | Determines whether declarations of the same identifier in different scopes refer to the same entity. |

---

## Local Variables

* Have **block scope** — visible from the point of definition to the end of the block.
* Have **automatic storage duration** — created at definition, destroyed when the block ends.
* Have **no linkage** — the identifier refers to a unique object or function.

---

## Global Variables

* Declared **outside any function** (usually at the top of a file, below includes).
* Belong to the **global namespace**.
* Have **static duration** — created when `main()` begins and destroyed when the program ends.
* Are **zero-initialized by default**.

```cpp
int g_x; // Automatically zero-initialized
```

* Can also be declared `const` or `constexpr`.

### Shadowing Global Variables

A local variable can **shadow** a global variable (similar to how a local variable can be shadowed):

```cpp
int g_x = 5;

void foo() {
    int g_x = 10; // Shadows the global variable

    std::cout << g_x;   // Prints 10
    std::cout << ::g_x; // Prints 5 (global scope)
}
```

---

## Linkage

### Recap

* Local variables → **no linkage**
* Global variables and functions → **internal** or **external linkage**

---

### Internal Linkage

* Identifiers with internal linkage are accessible **only within a single translation unit** (source file).

* Two identifiers in different source files with the same name **do not violate the One Definition Rule (ODR)**.

* To give a global variable **internal linkage**, use `static`:

```cpp
static int g_x{};
```

* Constant global variables have **internal linkage by default**.
* Non-constant globals and functions have **external linkage by default** but can be made internal using `static`.

> ✅ **Tip:** Prefer unnamed namespaces (see below) for internal linkage instead of `static`.

---

### External Linkage

* Identifiers with **external linkage** can be seen and used from multiple files.
* Functions have external linkage by default.
* Global variables can also have external linkage using `extern`:

```cpp
extern constexpr int g_x {5}; // const globals normally have internal linkage
```

* To **forward declare** an external variable:

```cpp
extern constexpr int g_x; // Declaration
constexpr int g_x {5};    // Definition
```

* You usually omit `extern` for non-const globals to avoid compiler warnings.

### Initialization Order Issues

* **Static variables** (including globals) are initialized **before `main()`** starts.
* **Static initialization** occurs first:

  * Constexpr initializers → constant initialization.
  * No initializer → zero initialization.
* **Dynamic initialization** follows for non-constexpr initializers.

> Rhe order of dynamic initialization **between translation units is undefined**.
> Avoid dependencies between global objects across different source files.

---

## Inline Functions

### Motivation

Each function call incurs overhead — parameters, return addresses, etc. must be managed on the stack.

```cpp
int min(int x, int y) {
    return (x < y) ? x : y;
}
```

The compiler may optimize this by **expanding the function inline**:

```cpp
std::cout << ((5 < 3) ? 5 : 3);
```

Modern compilers decide automatically when to inline a function — the `inline` keyword is now mostly **a linkage specifier**, not a request.

### Modern Meaning

The `inline` keyword now means **multiple identical definitions are allowed across translation units** without violating the ODR.

Inline variables behave similarly — they can be defined in multiple files as long as all definitions are identical.

---

### Example — Inline Constants Header

Old C++ style:

* Define constants in a namespace inside a header with guards.
* Every inclusion creates a new copy in each translation unit → recompilation required if constants change.

Better structure:

* Define constants with **external linkage** in a source file.
* Provide **forward declarations** in a header.
* However, constants can’t be declared `constexpr` in forward declarations — limiting compile-time optimization.

**Modern solution:** Inline variables.

```cpp
// constants.h
#ifndef CONSTANTS_H
#define CONSTANTS_H

namespace constants {
    inline constexpr double gravity { 9.8 };
    inline constexpr int max_score { 100 };
}

#endif

// main.cpp
#include "constants.h"
std::cout << constants::gravity;
```

* Only one copy of each constant exists program-wide.
* Constants are usable in `constexpr` expressions.
* Still, changing header content requires recompilation of dependent files.

---

## Static Local Variables

Applying `static` to a **local variable** gives it **static duration** instead of automatic duration.

* Zero-initialized at program start.
* Re-initialized the first time the definition is encountered.
* Retains its value between function calls.

Example:

```cpp
int getDatabaseValue() {
    static int value = queryDatabase(); // Initialized once
    return value; // Reuses cached value
}
```

This avoids repeated expensive operations (like database reads).

---

## Using Declarations

`using` allows you to access namespace members without qualification:

```cpp
using namespace std;
cout << "Hello World"; // No std:: prefix
```

> Generally considered **bad practice** — pollutes the global namespace.
> Prefer `std::cout` explicitly for clarity and safety.

---

## Special Namespaces

### Anonymous (Unnamed) Namespaces

```cpp
namespace { // No name!
    void helperFunction() {}
}
```

* Treated as part of the **parent namespace**.
* All identifiers within have **internal linkage**.
* Useful alternative to marking many globals/functions as `static`.
* You can call functions defined inside an unnamed namespace normally (no scope resolution needed).

---

### Inline Namespaces

```cpp
inline namespace V1 {
    void foo();
}

namespace V2 {
    void foo();
}

void doSomething() {
    V1::foo(); // Calls V1 version
    V2::foo(); // Calls V2 version
    foo();     // Calls V1::foo implicitly
}
```

* Members of an inline namespace are treated as part of the **parent namespace**.
* Inline namespaces don’t affect linkage — used mainly for **versioning APIs**.
* Enables gradual upgrades (e.g., new versions of functions) while keeping backward compatibility.

---

## Summary

* **Scope** → where an identifier is visible.
* **Duration** → how long a variable exists in memory.
* **Linkage** → whether multiple declarations refer to the same entity.
* **Namespaces** organize and isolate identifiers.
* Use `static` for internal linkage or static duration.
* Use `inline` for constants or functions defined in multiple translation units.
* Avoid global mutable variables and `using namespace std;`.
* Use **anonymous** namespaces for internal linkage and **inline** namespaces for versioning.
