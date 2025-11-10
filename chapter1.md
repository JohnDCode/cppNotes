# Chapter 1 — C++ Basics

## Objects and Variables

* When a program is loaded into RAM at runtime, **literals** are loaded into RAM as well.

* Objects represent regions of storage (typically in RAM or a CPU register).

* An object with a name is a **variable**.

* At runtime, an object is given a storage location (memory is allocated).

* Multiple variables of the same type can be defined in a single statement:
```cpp
int a, b;
```

* Separate declarations are also valid, but may not be best practice:
```cpp
int a; double b; // Correct / Valid but is not best practice
```

### Variable Assignment

* A variable can be assigned a value after it has been defined using the `=` operator:
```cpp
int width;
width = 5;
```

* By default, the assignment operator **copies the right-hand side** to the variable on the left-hand side.

### Variable Initialization

* Combining definition and assignment into a single statement is called **initialization**.
* 5 ways to initialize variables:

#### 1. Default Initialization

* No initialization is performed automatically for fundamental types.
* Example:
```cpp
int a;
```
* Leaves value **indeterminate**.

#### 2. Copy Initialization

* Inherited from C.
* Example:
```cpp
int width = 5;
```
* Used in cases like returning from a function by value or passing arguments by value.

#### 3. Direct Initialization

* Example:
```cpp
int width ( 5 );
```
* Also used in `static_cast`.

#### 4. List Initialization (Direct List Initialization)

* Best method.
* Narrowing conversions are **not allowed**.
* Example:
```cpp
int width {5};
```

#### 5. Zero Initialization

* List initialization with empty braces.

* Example:
```cpp
int width {};
```

* Defaults to value 0.

* Can also explicitly put 0 in braces for readability.

* **Instantiation** / **Instantiation** means a variable has been created (allocated) and initialized.

* `[[maybe_unused]]` attribute can be used to specify that a variable may not be used, preventing compiler warnings.

### Nomenclature Recap

* **Initialized**: object is given a known value at the point of definition.

* **Assignment**: object is given a known value after definition.

* **Uninitialized**: object does not have a known value yet.

* Using an uninitialized variable results in **undefined behavior**.

* C++ doesn't define what happens; anything can occur (different results, crash, etc.).

* Also be aware of:

  * **Implementation-defined behavior**: behavior up to the implementation; must be documented.
  * **Unspecified behavior**: behavior up to the implementation; does not have to be documented.

* General rule: **avoid all three** whenever possible.

## Input/Output

* The `<iostream>` header provides input/output functionality.

```cpp
std::cout << "Hello World!";
```

* `cout` is **buffered**: text is first sent to a buffer before being output to the console.

* `std::endl`:

  * Outputs a newline character.
  * Flushes the buffer (can be slow and unnecessary).

* `cin` is another predefined variable that lets us read character input from the keyboard.

* `cin` is also **buffered**.

* `<<` and `>>` act as **stream insertion** and **extraction operators** to deliver or remove content from the buffer.

---
