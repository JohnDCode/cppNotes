# Chapter 6 - Operators

An **operation** is a mathematical process involving zero or more input values (**operands**) that produces some new value via an operation denoted by a construct named an **operator**.

Example: `2 + 3` results in `5`.

---

## Precedence

Operators with higher precedence are grouped first (following **PEMDAS** in math). If operators with the same precedence level are adjacent, **associativity** tells the compiler whether to evaluate from left to right or right to left.

However, the **order of evaluation of operands** is mostly unspecified (undefined behavior):

```cpp
int fizz(int a) {
    std::cout << a;
    return a;
}

void buzz(int a, int b) {
    ;
}

int main() {
    buzz(fizz(5), fizz(3));
}
```

You can’t guarantee whether this prints `5` or `3` first — the order is unspecified.

---

## Arithmetic Operators

| Operator                  | Example  | Description                   |
| ------------------------- | -------- | ----------------------------- |
| Unary plus                | `+x`     | Value of x                    |
| Unary minus               | `-x`     | Negation of x                 |
| Addition                  | `x + y`  | x plus y                      |
| Subtraction               | `x - y`  | x minus y                     |
| Multiplication            | `x * y`  | x multiplied by y             |
| Division                  | `x / y`  | x divided by y                |
| Remainder                 | `x % y`  | Remainder of x divided by y   |
| Addition assignment       | `x += y` | Add y to x                    |
| Subtraction assignment    | `x -= y` | Subtract y from x             |
| Multiplication assignment | `x *= y` | Multiply x by y               |
| Division assignment       | `x /= y` | Divide x by y                 |
| Remainder assignment      | `x %= y` | Store remainder of x / y in x |

Notes:

* Integer division with a divisor of 0 → **undefined behavior**.
* Dividing by floating point 0.0 → **implementation defined**, but usually results in **NaN** or **Inf** under IEEE754.

---

## Modifying Operators

Some operators modify operand values directly — e.g., assignment (`=`), arithmetic assignment (`+=`, `-=`, etc.), and increment/decrement (`++`, `--`).

C++ does **not** have a power operator. `^` is **bitwise XOR**, not exponentiation — use the standard library `pow` instead.

| Operator          | Name | Example | Description                               |
| ----------------- | ---- | ------- | ----------------------------------------- |
| Prefix increment  | ++   | ++x     | Increment x, then return x                |
| Prefix decrement  | --   | --x     | Decrement x, then return x                |
| Postfix increment | ++   | x++     | Copy x, increment x, then return the copy |
| Postfix decrement | --   | x--     | Copy x, decrement x, then return the copy |

Side effects of these operators include the UB behavior described above, concerning the order in which operators are evaluated.

```cpp
int x = 1;
int y = x + ++x // is '++x' evaluated first or is 'x'
```

The result is undefined because the order of evaluation is unspecified.

---

## Comma Operator

The **comma operator** allows multiple expressions where one is expected. It evaluates the left operand, then the right, returning the right operand’s value.

```cpp
std::cout << (++x, ++y); // Increments both but only ++y is printed (right operand)
```

* Avoid this operator unless necessary (mainly useful in `for` loops).
* Be aware that `,` used as a **separator** (e.g., in function parameters) does **not** invoke the comma operator.

---

## Conditional Operator (`? :`)

Format: `c ? x : y`

If condition `c` is true, evaluate `x`; otherwise, evaluate `y`.

It’s effectively a single-line `if` statement and can be used anywhere an expression is accepted.

Guidelines:

1. Parenthesize the entire conditional expression when part of a larger expression.
2. Optionally parenthesize the condition itself if it contains operators.

```cpp
int x { 2 };
std::cout << (x < 0) ? "negative" : "non-negative";
```

The above produces unexpected results (0) unless parentheses are applied properly.

Also note: the second and third operands must be of matching or implicitly castable types.

---

## Relational Operators

| Operator         | Example  | Meaning        |
| ---------------- | -------- | -------------- |
| Greater than     | `x > y`  | true if x > y  |
| Less than        | `x < y`  | true if x < y  |
| Greater or equal | `x >= y` | true if x >= y |
| Less or equal    | `x <= y` | true if x <= y |
| Equality         | `x == y` | true if x == y |
| Inequality       | `x != y` | true if x != y |

Notes:

* Avoid directly comparing floating-point numbers — use `std::abs` to check if they’re within a small **epsilon** range.
* `epsilon` represents the smallest difference considered effectively equal.

---

## Logical Operators

| Operator | Keyword | Example    | Description                              |
| -------- | ------- | ---------- | ---------------------------------------- |
| !        | not     | `!x`       | true if x is false                       |
| &&       | and     | `x && y`   | true if both x and y are true            |
| \|\|     | or      | `x \|\| y` | true if either x or y (or both) are true |

Although `and`, `or`, and `not` keywords exist, **they are not preferred** in typical C++ code.

Remember **short-circuit evaluation** and **De Morgan’s Laws** apply.
