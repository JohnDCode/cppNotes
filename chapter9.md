# Chapter 9 - Error Detection and Handling

In testing your code, ensure you have total test coverage.  
**Code coverage** refers to how much of the source code is executed while testing.

There are different types of coverage:

- **Statement coverage**: percentage of statements executed during tests.  
- **Branch coverage**: percentage of branches (if/else) executed during tests.  
- **Loop coverage**: tests that a loop works for 0, 1, and 2 iterations (by induction, if it works for two, it should work for all others).

---

## Unit and Integration Testing

**Unit testing** involves testing a small part of code in isolation to ensure that "unit" behaves correctly.  
Each unit test ensures that a particular behavior of the unit is correct.

**Integration tests** ensure that individually tested units work properly together when integrated.

---

## Defensive Programming

Practice **defensive programming**, ensuring that all possible use cases are tested.

```cpp
void printIntDivision(int x, int y)
{
    if (y != 0) {
        std::cout << x / y;
    } else {
        std::cout << "Error: Could not divide by zero\n";
    }
}
```

Will cover exceptions later, but for now, reporting errors can be done by using something like a **sentinel value** — a value that has some special meaning in the context of a function or algorithm.  
For example, a sentinel return value can indicate success/failure (like the `0`/`nonzero` status code returned from `main`).

Use **`std::cerr`** for logging messages that may be helpful for diagnosing issues.

---

## Nomenclature

- **Precondition** – A condition that must be true before a section of code executes.  
- **Invariant** – A condition that must be true while a section of code executes.  
- **Postcondition** – A condition that must be true after a section of code executes.

---

## Assertions

Assertions are expressions that will be true unless there is a bug in the program.  
If the condition evaluates to false, an error message is displayed and the program terminates via `std::abort`.

Runtime assertions are implemented via the **`assert`** preprocessor macro from the `<cassert>` header.

```cpp
assert(divisor != 0.0);
```

Add a string literal joined by a logical AND to make error messages more descriptive:

```cpp
assert(divisor != 0.0 && "Can not divide by 0");
```

- String literals always evaluate to true, so the only way this fails is if the original statement fails.

However, asserts should only be used in **development builds**, since production code should already be tested.

To control assertions:

- `#define NDEBUG` disables asserts.  
- `#undef NDEBUG` re-enables asserts.

---

## Compile-Time Assertions

`static_assert` is similar to `assert`, but is checked at **compile-time** rather than runtime.  
It is a **keyword**, so no header is needed.

Syntax examples:

```cpp
static_assert(divisor != 0, "Can not divide by 0");
```

Notes on `static_assert`:

- The condition must be a **constant expression** due to compile-time evaluation.  
- It can appear **anywhere**, even in the global namespace.  
- It cannot be deactivated in release builds (unlike `assert`).  
- There is **no runtime cost**, since it’s checked by the compiler.
- Due to these effects, should use them liberally.
