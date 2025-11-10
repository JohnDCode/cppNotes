# Chapter 10 — Type Conversion, Type Aliases, and Type Deduction

## Implicit and Explicit Type Conversion

* A type conversion produces a temporary object of the target type before assigning it to the destination object.
* Failed conversions lead to compiler errors.
* Direct list initialization does not allow narrowing conversions.
* C++ allows both:

  * Implicit upcasting / widening
  * Downcasting / narrowing

---

## Standard Conversions

* Defines how various fundamental types convert to other funamental types

### Numeric Promotions

* Efficient, value-preserving widening conversions among numeric types.
* Removes redundancy (e.g., std::cout can treat all numeric types uniformly).

Examples of Promotions:

* float -> double
* signed char / short -> int
* bool -> int

```cpp
short s = 42;
float f = 3.14f;
double result = s + f; // short promoted to double before addition

bool b = true;
int i = b; // bool promoted to int, i = 1
```

### Numeric Conversions

* Involves conversions between other integral and floating-point types.
* Some of these are unsafe conversions and can cause:

  * Lossy conversions: data lost (e.g., double -> int)
  * Reinterpretive conversions: meaning changes but bits preserved (e.g., signed int -> unsigned int)

```cpp
double d = 9.8;
int x = d; // lossy conversion: x = 9

int n = -5;
unsigned int u = n; // reinterpretive: wraps around to large positive number
```

* Overflow is well-defined for unsigned values, but undefined behavior for signed values.
* Thus, do not use implicit conversion for narrowing conversions.
* Use constexpr + brace initialization to detect narrowing conversions at compile time.

```cpp
constexpr int a{3.14}; // Compile-time error: narrowing from double to int
constexpr double b{3.14}; // OK
```

### Qualification Conversions

* Add or remove const or volatile.

```cpp
const int y = 10;
int* py = const_cast<int*>(&y); // removes constness (dangerous, but allowed)
```

### Value Transformations

* Conversions that change value category (e.g., from lvalue to rvalue).

### Pointer Conversions

* Convert std::nullptr_t to pointer types, or between compatible pointer types.

```cpp
int* ptr = nullptr; // nullptr_t implicitly converts to pointer
double value = 5.0;
double* dp = &value;
void* vp = static_cast<void*>(dp); // pointer conversion to void*
```

---

## Conversions in Expressions

* Operators often require operands to be of matching types.
* C++ applies usual arithmetic conversions automatically to make types compatible.

Rules:

1. Convert integral -> floating if needed.
2. Promote smaller integral types.
3. Resolve unsigned/signed combinations.
4. Convert to type of higher rank.

Rank (highest -> lowest): long double -> double -> float -> long long -> long -> int

```cpp
int i = 2;
double d = 3.5;
auto sum = i + d; // i promoted to double, sum = 5.5

unsigned int u = 5;
int j = -3;
auto result = u + j; // signed + unsigned conversion rules applied
```

---


## Explicit Conversions

### C++ Cast Operators

| Cast         | Description                                   |
| ------------ | --------------------------------------------- |
| static_cast  | Compile-time conversion between related types |
| const_cast   | Adds or removes const / volatile              |
| dynamic_cast | Runtime conversion in inheritance hierarchies |
| C-style cast | Combination of above (less safe)              |

```cpp
// static_cast
int i = 42;
double d = static_cast<double>(i); // safe compile-time conversion

// const_cast
const int x = 10;
int* px = const_cast<int*>(&x); // remove const

// dynamic_cast
struct Base { virtual ~Base(){} };
struct Derived : Base {};
Base* b = new Derived();
Derived* dptr = dynamic_cast<Derived*>(b); // runtime check

// C-style cast
int n = 10;
double d = (double)n; // C-style cast
```

---

## Type Aliases

### Using Declarations

```cpp
using Distance = double;
Distance d = 3.5; // same as double
```

* Compiler substitutes the aliased type.
* Has same scope rules as identifiers.
* Useful, what if in the above example we wanted to change all distances to type long double? Would have to change the types of all distances, now can just change the aliased type.


### Fixed-Width Integer Types & Aliases

* Fixed-width types (std::uint32_t, std::size_t, etc.) are aliases for fundamental types, as sizes of the fundamental data types are platform specific.
* Aliases simplify readability and maintenance.
* Also ensures code is platform independent:

```cpp
#ifdef INT_2_BYTES
using int16_t = int;
#else
using int32_t = int;
#endif
```

```cpp
#include <cstdint>

int a = 10000 // int is aliased to int16_t or int32_t based on the platform, but works on both
```


### Typedef (Old Style -> Not Best Practice Anymore)

```cpp
typedef double Speed;
Speed s = 12.5;
```

### Strong Typedefs (Other Languages)

* Some languages support strong typedefs that prevent mixing aliased and base types — C++ does not (without wrappers).

---

## Type Deduction (auto)

### Basics

```cpp
auto f = 3.14f; // deduces float
auto x = 42;    // deduces int
```

* Must be initialized at declaration.
* Deduction uses the initializer expression.

### With String Types

```cpp
using namespace std::string_literals;
auto str = "hello"s; // deduces std::string
```

* The deduced type by compiler for strings is usually weird, so need the s literal suffix.

### Function Return Type Deduction

* Can use type deduction on the return types for functions, but must fully implement the function before use.

```cpp

auto foo();

int main() {
    std::cout << foo() << "\n"; // Compiler error, here the compiler does not know what type foo returns, can only deduce once it has an implementation
}

auto foo() {
    return 1.0
}
```

* Function type deduction is main reason for using trailing return type syntax.

```cpp
#include <type_traits>

std::common_type_t<decltype(x), decltype(y) compare(int x, double y) // Compiler error, compiler doesn't see what x and y are until after the return type, error
{
    return x < y ? x : y;
}


auto compare(int x, double y) -> std::common_type_t<decltype(x), decltype(y)> // Compiler sees parameters first, thus knows what that return type means by the time it gets to it
{
    return x < y ? x : y;
}
```

---

## Additional Notes Gathered From the Chapter

* Volatile keyword tells the compiler that the variable may be modified externally (hardware or another thread), preventing certain optimizations.
* std::memcpy copies a specified number of bytes between memory locations.

---

## Summary

* Implicit conversions happen automatically; explicit ones need cast operators.
* Prefer static_cast and using over older alternatives.
* Type deduction simplifies code but requires careful initialization.
* Use aliases for clarity, maintainability, and platform independence.
