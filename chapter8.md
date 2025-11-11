# Chapter 8 – Control Flow

* This chapter covers control flow in C++. Since control flow is a basic programming concept, the focus here is on **syntax** and **C++-specific features** (e.g., `constexpr if`).

---

## Execution Path

An **execution path** is the sequence of statements that the CPU executes.  
C++ provides several **control flow statements** that can alter this path.

Use **blocks** (groups of statements enclosed in braces) to specify multiple statements as a single execution path.

---

## Conditional Statements

**Syntax:**

```cpp
if (condition)
    statement;
else if {
    statement_2;
    statement_3;
} else {
    statement_4;
}
```

### Notes
* Consider whether the conditional in an `if` statement can be evaluated **at compile time** (i.e., it’s a constant).
* Compiling unnecessary runtime branches wastes space and time.
* Use the `constexpr` keyword to tell the compiler to evaluate an `if` condition at compile time.

**Example (constexpr if):**

```cpp
constexpr int x {5};
if constexpr (x == 5) {
    statement;
} else {
    statement_2;
}
```

At compile time, the entire `if`/`else` structure is replaced by the chosen branch.  
Some compilers optimize constant conditionals automatically, but `constexpr` guarantees it.

---

## Switch Statement

**Syntax Example:**

```cpp
switch (x)
{
case 1: // not indented from switch statement
    std::cout << "One";
    break;
case 2:
    std::cout << "Two";
    break;
case 3:
    std::cout << "Three";
    [[fallthrough]]
default:
    std::cout << "Unknown";
    break;
}
```

### Notes
* Always use `break` or `return` where appropriate.
* Use the `[[fallthrough]]` attribute to **explicitly indicate intentional fallthrough**.
* Remember: cases must be integral or enum constants.

---

## Goto Statements

**Syntax Example:**

```cpp
goto point;

point:
    statement
    statement_2
```

### Notes
* Generally **not recommended**, as they can lead to *spaghetti code* (unstructured, difficult-to-follow execution paths).
* Statement labels have **function scope**, meaning they can be seen anywhere in the function—even before their declaration.
* Both the `goto` and the label must appear in the **same function**.

---

## Loops

### General Notes
* Remember the `break` and `continue` keywords.
* Use **signed** loop variables to avoid overflow errors (e.g., decrementing an unsigned variable below 0 wraps around to a large value).
* Beware of **off-by-one** errors.
* Prefer `<` or `>` comparisons over `!=` to ensure the loop terminates even if the iterator “jumps” a value.
* Conditions can be omitted in all loop types with a semicolon.

---

### While Loop

```cpp
int i{};
while (i <= 5) {
    foo(i);
    ++i;
}
```

---

### Do-While Loop

```cpp
int i{};
do {
    foo(i);
    ++i;
} while (i <= 5);
```

---

### For Loop

```cpp
for (int i{0}; i <= 5; ++i) {
    foo(i);
}
```

* The initialization and end expressions can be omitted.
* **Range-based for loop** (for containers like arrays or vectors):

```cpp
for (int i : collection) {
    std::cout << i << "\n"; // Outputs all integers in an integer array or vector
}
```

**Execution order:**
1. Initialization executes once at the beginning.
2. Condition executes.
3. If true → body executes.
4. End-expression executes.
5. Condition checks again.

You can define multiple initialization statements or expressions using the **comma operator**:

```cpp
for (int x{ 0 }, y{ 9 }; x < 10; ++x, --y) { ... }
```

---

## Halting Programs

C++ provides several functions to stop program execution.

### `std::exit()`

* Terminates the program **normally**, performing cleanup operations:
   Destroys static objects
  - Flushes and closes open files
  - Returns control to the OS with a specified return code

```cpp
std::exit(0);
```

* `std::exit()` is implicitly called after `main()` returns but can also be called manually.
* Requires the `<cstdlib>` header.
* Does **not** destroy local (automatic) variables in the current function or its call stack.

---

### `std::atexit()`

Registers a function to be called automatically before program termination (useful for cleanup):

```cpp
void cleanup() { ... }
std::atexit(cleanup);
std::exit(0);
```

* Be careful: in multithreaded programs, `std::exit()` may cause crashes if static objects are still in use by other threads.

---

### Quick Exit Functions

C++ provides a “fast termination” variant that skips cleanup of static objects:

* `std::quick_exit()` — terminates normally but without cleanup.
* `std::at_quick_exit()` — registers a function to call before `quick_exit`.

---

### `std::abort()` and `std::terminate()`

* `std::abort()` — terminates abnormally with **no cleanup** (used when assertions fail).
* `std::terminate()` — used with exceptions; by default, it calls `std::abort()`.

---

**Note:**  
This chapter also covers **random number generation (RNG)**, but that section is omitted here.
