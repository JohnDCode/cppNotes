# Chapter 4 — Fundamental Data Types

## Overview

* Variables are names for pieces of memory that can be used to store information.
* Computers have **RAM** available for programs to use.
* When a variable is defined, **memory is allocated** for that variable.

---

## Memory and Representation

* The smallest **unit of memory** is a **bit**, but the smallest **addressable unit** is a **byte** (8 bits).
* Each byte has a unique **memory address**.
* The **type** of a variable tells the compiler how to interpret the raw system memory.
* The type information is stored in a **symbol table**, which the compiler uses to interpret values properly.

---

## Categories of Types

C++ defines three broad categories of types:

1. **Fundamental data types**
2. **User-defined types**
3. **Types provided by the C++ Standard Library**

---

## Fundamental Data Types

| Type                                                                         | Category             | Description                              | Example   |
| ---------------------------------------------------------------------------- | -------------------- | ---------------------------------------- | --------- |
| `float`, `double`, `long double`                                             | Floating-point       | Numbers with fractional parts            | `3.14159` |
| `bool`                                                                       | Integral (Boolean)   | True or false values                     | `true`    |
| `char`, `wchar_t`, `char8_t` (C++20), `char16_t` (C++11), `char32_t` (C++11) | Integral (Character) | Single character of text                 | `'c'`     |
| `short int`, `int`, `long int`, `long long int` (C++11)                      | Integral (Integer)   | Whole numbers (positive, negative, zero) | `64`      |
| `std::nullptr_t` (C++11)                                                     | Null Pointer         | Represents a null pointer                | `nullptr` |
| `void`                                                                       | Void                 | Represents the absence of a type         | `n/a`     |

---

## Notes on Long and Short

* `long` was introduced as an **extension** to `int`:

  * `long` ≡ `long int`
  * `long long` ≡ `long long int`
  * `long double` extends `double`
* The C++ standard **guarantees only the ordering**:

  ```
  short ≤ int ≤ long ≤ long long
  ```
* Typical sizes (on Windows):

  ```
  short: 16 bits
  int: 32 bits
  long: 32 bits
  long long: 64 bits
  ```
* `short` and `long` are **type modifiers** for `int`.
* There are no such types as `short short` or `short double`.

---

## Integral Types

* The term **integral** means “integer-like.”
* It includes standard integer types (`short`, `int`, `long`, `long long`, and their unsigned versions) and types handled as integers (`bool`, `char`, etc.).
* Many standard library types use the suffix `_t`, meaning “type.”

---

## The `void` Type

* `void` is an **incomplete type** — it represents the **absence of a type**.
* Common uses:

  * As a **function return type** to indicate no return value.
  * As a **function parameter list** in C (deprecated in C++):

    ```cpp
    void foo(void); // same as void foo();
    ```

---

## Type Sizes and the `sizeof` Operator

The C++ standard does **not guarantee exact sizes** for fundamental types — only minimums and relationships.

### Standard Guarantees

* Every object must occupy **at least 1 byte** (so each object has a distinct address).
* A byte must be **at least 8 bits**.
* Minimum sizes:

  ```
  char      ≥ 8 bits
  short     ≥ 16 bits
  int       ≥ 16 bits
  long      ≥ 32 bits
  long long ≥ 64 bits
  ```
* `char` and `char8_t` are exactly **1 byte**.

### The `sizeof` Operator

* Returns the **size in bytes** of a type or variable.

```cpp
sizeof(char); // always 1 byte
sizeof(int);  // implementation-defined
```

* `sizeof` does **not** include dynamically allocated memory.

---

## Static vs Dynamic Memory Allocation

* **Statically allocated memory**: determined at compile time (stack).
* **Dynamically allocated memory**: determined at runtime (heap or stack).

Examples:

* Recursive function calls allocate **stack frames** dynamically.
* Memory allocated on the heap must be **manually deallocated** (freed).

---

## Signed and Unsigned Integers

* **Signed integers** (default) can represent positive and negative numbers.

  * Example: `signed short s;` is equivalent to `short s;`
  * For an `n`-bit signed integer:

    ```
    Range = -2^(n-1) to 2^(n-1) - 1
    ```

    * Example: 8-bit → −128 to 127
  * Overflow results in **undefined behavior**.

* **Unsigned integers** represent only non-negative values:

  ```
  Range = 0 to 2^n − 1
  ```

  * Example: 8-bit → 0 to 255
  * Overflow is **well-defined**:

    * Value wraps around modulo (max + 1)
    * Example: `256 % 256 = 0`
  * Assigning negative values wraps around to the top of the range.

* **Best Practice:** Prefer signed integers unless unsigned behavior is explicitly required (e.g., bit manipulation).

---

## Fixed-Width Integers (`<cstdint>`)

* Fundamental type sizes vary by implementation, so C++ provides **fixed-width integer aliases**.

```cpp
#include <cstdint>

std::int8_t   // 8-bit signed
std::uint8_t  // 8-bit unsigned
std::int16_t  // 16-bit signed
std::uint16_t // 16-bit unsigned
std::int32_t
std::uint32_t
std::int64_t
std::uint64_t
```

### Notes

* `std::int8_t` and `std::uint8_t` often behave like `char`.
* These are **aliases** for native types (e.g., `int32_t` → `int`).
* Not guaranteed to exist on all architectures (requires matching native type).

---

## Fast and Least Integer Types

* `<cstdint>` also provides:

```cpp
std::int_fast8_t, std::int_fast16_t, std::int_fast32_t, std::int_fast64_t
std::int_least8_t, std::int_least16_t, std::int_least32_t, std::int_least64_t
```

* `_fast` types → **fastest type** at least that size.
* `_least` types → **smallest type** at least that size.
* There also exists unsigned verisons of these with **uint**

* These vary between systems — may affect portability.

---

## `std::size_t`

* The type returned by the `sizeof` operator.
* Defined in `<cstddef>`:

```cpp
#include <cstddef>
std::size_t size = sizeof(int);
```

### Properties

* Implementation-defined **unsigned integral type**.
* Guaranteed to be unsigned and at least **16 bits**.
* Usually matches the **address width**:

  * 32-bit program → 32-bit `size_t`
  * 64-bit program → 64-bit `size_t`

### Implications

* `size_t` imposes an **upper limit on object size**.
* Example:

  * If `size_t` is 4 bytes → max value `2^32 - 1`
  * Thus, a single object cannot exceed ≈4.3 GiB.

---

## Floating-Point Numbers

* Represent numbers with **fractional components**.
* C++ has three fundamental floating-point types:

```cpp
float
double
long double
```

### IEEE 754 Standard

* Defines floating-point representation.
* Typical sizes:

  * `float` → 4 bytes
  * `double` → 8 bytes
  * `long double` → platform-dependent (use cautiously)

### Precision

| Type     | Significant Digits (Approx.) |
| -------- | ---------------------------- |
| `float`  | 6–9 (typically 7)            |
| `double` | 15–18 (typically 16)         |

### Floating-Point Representation

* Stored as repeating binary fractions — cannot represent all decimal values exactly.
* Leads to **rounding errors**.

### Special IEEE 754 Values

* `+Inf`, `-Inf` — infinity
* `NaN` — Not a Number
* Signed zero (`+0.0`, `-0.0`)

### Storage Formula

```
(-1)^sign × (1 + fraction) × 2^(exponent - bias)
```

* Bias is constant:

  * `float`: 127
  * `double`: 1023

---

## Character Types

* Character types are **integral types** representing encoded characters.
* Most systems use **ASCII** for basic characters.

### ASCII Overview

* Characters mapped to integers 0–127.

  * `0` = null character
  * `13` = carriage return
  * `65` = 'A'
  * `97` = 'a'

### Character Literals

```cpp
char ch = 'A';
```

* Single quotes (`' '`) denote **character literals**.

### Unicode and Extended Character Sets

* Modern systems use **Unicode** for global character representation.
* Unicode includes over **144,000 characters**.

| Encoding | Description                   | Size      |
| -------- | ----------------------------- | --------- |
| UTF-8    | Variable-width (8-bit units)  | 1–4 bytes |
| UTF-16   | Variable-width (16-bit units) | 2–4 bytes |
| UTF-32   | Fixed-width (32-bit units)    | 4 bytes   |

C++ provides corresponding character types:

* `char8_t` — UTF-8
* `char16_t` — UTF-16
* `char32_t` — UTF-32

---

## Summary

* Variables are memory locations interpreted according to their types.
* C++ fundamental types include integers, floating-point numbers, characters, `bool`, `void`, and `nullptr_t`.
* Size and representation of types depend on the implementation, but relationships are guaranteed.
* Prefer fixed-width or standard types (`<cstdint>`, `size_t`) for portability.
* Understand rounding and precision limits in floating-point arithmetic.
* Characters are stored as integers — often ASCII or Unicode mappings.
