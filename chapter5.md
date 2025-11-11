# Chapter 5 – Constants and Strings


## Constants Overview

**Definition:**  
Constants are values that **cannot be changed** during program execution.

**Types of constants:**
1. **Named constants** – constants associated with an identifier (symbolic constants)
2. **Literal constants** – constants inserted directly into code, not associated with identifiers

### Ways to define a named constant:
* Object-like macros (preprocessor)
* Constant variables (`const`)
* Enumerated constants

---

## Constant Variables (`const`)

* A **constant variable** cannot be changed after initialization
* Syntax: `const` on the left of type is conventional (instead of "east const")  

```cpp
const double EARTH_GRAVITY {9.8};
```

* Note: the type here is const double, not just double

* `const` is a **type qualifier**, modifying how the type behaves
* In C++23, the only type qualifiers are:
  - `const` – ensures value cannot change
  - `volatile` – tells the compiler the value may change at any time
* Naming conventions:
  - All caps with underscores → `EARTH_GRAVITY`
  - CamelCase with `k` prefix → `kEarthGravity`

### Function Parameters

* `const` can be applied to function parameters:

```cpp
void foo(const int x) { ... }
```

* Only meaningful for **pass by reference** or **pass by address**
* For pass by value, `const` is mostly redundant because the copy will be destroyed after the function

### Const Return Types

```cpp
const int foo(int x) { ... }
```

* For fundamental types, `const` return types are ignored by compiler
* For other types, `const` may prevent modifications to returned objects

### Benefits of `const`

* Reduces bugs
* Enables compiler optimizations
* Reduces complexity (value guaranteed not to change)
* Prefer `const` over object-like macros because macros have poor scoping rules

---

## Literal Constants

* Inserted directly into code
* Example:

```cpp
const double pi {3.14}; // 3.14 is a literal constant, pi is a symoblic / named constant
```

### Literal Type Deduction

| Value Type      | Example           | Deduced Type      |
|-----------------|-----------------|-----------------|
| Integer         | 5, 0, -3        | int             |
| Boolean         | true, false      | bool            |
| Floating-point  | 1.2, 0.0, 3.4   | double          |
| Character       | 'a', '\n'       | char            |
| C-style string  | "Hello, world!"  | const char[14]  |

### Literal Suffixes

* Used when the deduced type is not the desired type.

* **Integral**
  - `u` or `U` → unsigned int
  - `l` or `L` → long
  - `ul`, `uL`, `Ul`, `UL`, etc. → unsigned long
  - `ll` or `LL` → long long
  - `ull`, `uLL`, etc. → unsigned long long
  - `z` or `Z` → signed std::size_t (C++23)
  - `uz`, `uZ`, `Uz`, `UZ`, etc. → std::size_t (C++23)
* **Floating-point**
  - `f` or `F` → float
  - `l` or `L` → long double
* **Strings**
  - `s` → std::string
  - `sv` → std::string_view

* In most cases, suffixes aren’t necessary except `f` for floats. Use uppercase for readability.

### Scientific Notation

```cpp
double avagadro {6.02e23} // evaluated to --> 6.02 * 10^23
```

### Magic Numbers

* A magic number is a literal with **unclear meaning**, often needing clarification:

```cpp
int input; // implementation hid
int count {input * 6} // Why do we multiply by 6? --> 6 is a magic number
```

* Avoid magic numbers by using named constants

---

## Octal, Hexadecimal, and Binary Literals

```cpp
int x { 012 }; // preceding 0 indicates a octal value

int x { 0xF } // preceidng 0x indicates a hex value

std::int8_t x { 0b1 }; // preceding 0b indicates a binary value --> this crates 0000 0001 
```

* Internally, all of these literals are just type int (and thus their sizes are platform-dependent)

* Digit separator `'` can improve readability

```cpp
std::uint8_t bits { 0b0000'0001 };
```


---

## Compiler Optimizations

* Modern compilers perform optimizations while preserving **observable behavior** (as-if rule)
* Examples:
  - **Constant folding:** compiler evaluates expressions at compile-time  

```cpp
int x {5 + 4}; // replaced with --> int x {9};
```

  - **Constant propagation:** compiler replaces references to constants with the value  

```cpp
int x {5}
if (x > 5) // replaced with --> if (5 > 5)
```

* Using `const` helps compiler perform these optimizations

### Dead Code Elimination

* Removes code that is executed but has no effect
* Example: variable declared and initialized but never used

---

## Nomenclature

* **Compile-time constant:** value known at compile-time
  - Literals
  - Constant objects with compile-time initializers
* **Runtime constant:** value determined at runtime
  - Constant function parameters
  - Constant objects with non-constant initializers

---

## Compile-time Expressions

* **Constant expressions** can be evaluated at compile-time
* Use of such features is called **compile-time programming**
* Benefits:
  - Improved performance
  - Early bug detection
  - Consistent behavior across platforms
  - Avoid undefined behavior

* Constant expressions consist of:
  - Literals
  - Constant variables
  - Operators
  - Function calls (all evaluable at compile-time)

### Likelihood of Compile-time Evaluation

* All constant expressions CAN be evaluated at compile-time, but the compiler is not guarnteed to evaluate them at compile-time.
* Compilers will only always evaluate constant expressions when they are required to be evaluated at compile time (i.e initalizing a constexpr variable).
* Otherwise, this is implementation specific behavior:

- **Never:** non-constant expression, cannot determine values
- **Possibly:** non-constant expression, compiler may optimize
- **Likely:** constant expression in non-required context
- **Always:** constant expression in required context

---


## Recap: `const` vs `constexpr`

* A `const` variable with an integral type **and** a constant expression initializer can be used in constant expressions:

```cpp
const int x {5};
```

* **Important:** Using `const` **does not guarantee** that a variable is a constant expression.  
  ```cpp
  int x; // implementation hidden
  const int y {x} // x is not const at all, so how do we know if its const
  ```

```cpp
const int x { foo(); }
```

  - This is **semantically valid** as long as the value of `x` is not changed after initialization.
  - But how do we know if `x` is actually a constant expression?  
    - We have to check the initializer (`foo()`) and see if it is a `constexpr` function.
  - Therefore, using `const` alone **does not tell the compiler** that this variable can be used in other constant expressions.
  - Another limitation: `const` **cannot** be used to create compile-time constants with **non-integral types**.

---

## `constexpr`

* The `constexpr` keyword (short for **constant expression**) is used **in place of `const`** to tell the compiler that a variable **must be evaluated at compile-time**.
* `constexpr` variables **must** be initialized with a constant expression.
* `constexpr` can also be applied to **non-integral types**:

```cpp
constexpr double pi {3.14}; // This is a constant expression
```

* Summary of differences:
  - `const` → ensures a variable **cannot be changed** after initialization. Evaluation can occur at compile-time **or runtime**.
  - `constexpr` → ensures the variable **is evaluated at compile-time**.
* **Type note:** `constexpr` is **not actually part of the type**.  
  - For example, a variable defined as `constexpr int` is technically of type `const int`.

---

## `const` as Function Parameters

* `const` can be applied to function parameters as described above.
* Function parameters are initialized with the **arguments passed at runtime**, so their values are only known **at runtime**.
* Therefore, `const` function parameters are **runtime constants**, and `constexpr` **cannot** be used for function parameters (as constexpr is for compile-time constants / constant expressions).

---

> Later, `constexpr` functions will be discussed, which is a separate topic.
