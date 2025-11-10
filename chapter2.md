# Chapter 2 — C++ Basics: Functions and Files

## Function Basics

* Functions in C++ follow the format:
```cpp
int increment(int x) {

}
// ReturnType identifier(parameter list)
```

* Names of parameters are optional, but should document if unused.

* Example of unused parameter:
```cpp
void do(int /*x*/) {
    return 1;
}
```

* Useful when a parameter is no longer needed; allows function signature to remain compatible without rewriting every call.

## Definitions and Forward Declarations

* **Declaration**: tells the compiler about the existence of an identifier and its type information.

* **Definition**: a declaration that also implements the function or type, or instantiates a variable.

* A declaration means "pure declaration".

* A definition is a declaration that also implements or instantiates.

* Example: `int x;` is both a declaration and definition.

* Some identifiers require full definition before use; functions do not.

* **Forward declaration**: declare existence before definition.

* Example:
```cpp
int add(int x, int y);
```

### One Definition Rule (ODR)

1. Within a file, each function, variable, type, or template in a given scope can only have one definition. Different scopes do not violate this.
2. Within a program, each function or variable in a given scope can only have one definition. Programs may span multiple files. Internal linkage variables/functions are excluded.
3. Types, templates, inline functions, and inline variables can have identical duplicate definitions across files.

## Multiple File Programs

* C++ programs can be split into multiple files.
* Each source file can be compiled independently.
* Functions declared in one file and defined in another require a **function prototype**.
* Compiler satisfied by prototype; linker resolves the implementation.

## Naming Collisions

* Naming collisions occur when identifiers conflict.
* **Scope region**: area of code where declared identifiers are considered distinct from other scopes.
* Examples:

  * Function body
  * Namespace
* Namespaces provide **namespace scope**, isolating names from other scopes.
* Namespace can only contain declarations and definitions. Executable statements are only allowed inside definitions (like functions).
* Global namespace includes names not in any class, function, or other namespace (global scope).
* `std` is a namespace.
* Avoid `using namespace` directives; use explicit `std::` with scope resolution operator.

### Scope Intro

* Objects have scope (where the identifier can be seen and used in code) and is a compile time property.
* They also have a lifetime, which refers to the time between its creation and destruction. This happens at runtime, and is thus a runtime property.
* In Scope / Out of Scope refers to if an identifier can be used at that point in code.
* When an identifier goes out of scope, nothing in particular immediately happens.
* Use of an out of scope object results in UB.
* At some point after destruction, memory from the object is deallocated (freed).
* An identifier with local scope is usable from point of definition to the end of the innermost pair of curly braces (end of the block).

## Preprocessor

* Runs before compilation; processes code into **translation units**.

* **Preprocessor directives** start with `#` and end with newline.

* Strips comments, handles `#include` directives.

* `#include` replaces directive with the contents of the included file.

* Angled brackets `< >` indicate system headers; double quotes `" "` indicate user headers.

* `#define` creates macros (object-like and function-like). Avoid function-like macros.

* Object-like macros example:
```cpp
#define IDENTIFIER sub_text
```

* Conditional compilation directives: `#ifdef`, `#ifndef`, `#endif`.

  * `#ifdef` checks if identifier defined; if so, compiles enclosed code.
  * `#ifndef` checks if identifier is not defined; if so, compiles enclosed code.

* Header files propagate forward declarations.

* Including a header in multiple files can violate ODR if definitions duplicate.

* **Header guards** prevent duplicate inclusion:
```cpp
#ifndef UNIQUE_NAME
#define UNIQUE_NAME

// Definitions or declarations

#endif
```

* Ensures header is copied only once.
* Duplicate declarations are ok, but duplicate definitions are not, so we use a header guard.

* `#pragma once` is another option but not technically standard; use traditional header guards for portability and just because its technically the conventional method.

---
