# Chapter 3 — Debugging C++ Programs


## Error Nomenclature

* Syntax errors - errors that violate the grammar of the C++ language.
* Semantic errors - statement is syntactically valid, but violates other rules of the language (as in things that cause undefined behavior) or don't do what the programmer wants (logic errors).

```cpp
// Syntax errors
doubl x; // Spelled double wrong, not valid type
double 1x; // Names can't start with a number
return 0 // Missing semicolon at the end of a statement

// Semantic errors
// Note that no grammar rules are violated, we violated some other rule, as in we can't use a value to a variable that hasn't been declared
y = 5; // y not declared so cannot use assignment operator on it

```

* Note that this does not necessarily differentiate between compile time and runtime errors, that is a different set of nomenclature.

## Debugging Reminders

* Use print statements to observe control flow.
* Use an integrated debugger to do the following:
    * Step through your program line by line to observe control flow.
    * Use breakpoints to run code until it reaches a certain point, essentially stepping but quicker until you reach the point of interest.
    * Most integrated debuggers let you observe the value of variables in real time, as you step, so watch them as you step through your program.
    * The integrated debugger will also let you look at the call stack (which is a stack data structure used by the program to store information about the active subroutines or functions).

---
