# Chapter O - Bit Manipulation

## Memory and Bit-Level Representation

- For most architectures, the smallest addressable unit of memory is a **byte**.  
- Objects need to have **unique memory addresses**, so each must be at least one byte in size.  
- This can lead to **wasted memory** when the object’s data does not fill a full byte.  
  - Example: A boolean only needs 1 bit but occupies 1 byte (8 bits).

---

## Bit Manipulation

- **Bit manipulation** = modifying memory at the **bit level**.  
- When individual bits are used as boolean values, they are called **bit flags**.

### Defining Bit Flags

You can define bit flags using either `std::bitset` or unsigned integers:

Here

```cpp
std::bitset<8> mybitset  = 0b10101100;
std::uint8_t flags{ 0b0000'0101 }; // 8 bits in size means room for 8 flags
```

## Useful Member Functions of `std::bitset`

| Function | Description |
|-----------|--------------|
| `test()`  | Queries whether a bit is 0 or 1. |
| `set()`   | Turns a bit on (1). |
| `reset()` | Turns a bit off (0). |
| `flip()`  | Flips a bit from 0→1 or 1→0. |

- The argument for all these functions is the position of the bit to modify.

```cpp
std::bitset<8> bits{ 0b0000'1101 }; // we need 8 bits, start with bit pattern 0000 0101
bits.set(3);   // set bit position 3 to 1 (now we have 0000 1101)
```

### Querying Bitsets

| Function | Description |
|-----------|--------------|
| `size()` | Returns total number of bits. |
| `count()` | Returns how many bits are set to 1. |
| `all()` | Returns true if all bits are 1. |
| `any()` | Returns true if any bit is 1. |
| `none()` | Returns true if no bits are 1. |

---

## Performance and Memory Use

- The size of `std::bitset` is **optimized for speed**, not memory.  
- A `bitset` takes as much space as needed to store all bits, rounded up to the nearest `sizeof(size_t)` (typically 4 or 8 bytes).  
- If memory optimization is critical → use **unsigned integers** instead.

---

## Bitwise Operators

| Operator  | Name | Example  | Description  |
|-----------|------|----------|--------------|
| `<<` | Left shift | `x << n` | Shifts bits left `n` positions, new bits = 0 |
| `>>` | Right shift | `x >> n` | Shifts bits right `n` positions, new bits = 0 |
| `~` | Bitwise NOT | `~x` | Flips all bits |
| `&` | Bitwise AND | `x & y` | Bit = 1 if both bits are 1 |
| `\|` | Bitwise OR | `x \| y` | Bit = 1 if either bit is 1 |
| `^` | Bitwise XOR | `x ^ y` | Bit = 1 if bits differ |

- **Note:** `<<` and `>>` are the same symbols used for I/O stream operators — this is due to **operator overloading**.  
- Bitwise operators **promote operands** to `int` or `unsigned int` — this can affect results when using smaller types.  
- Results are returned as `int` or `unsigned int` → be careful when using **direct list initialization**, which forbids narrowing conversions.

---

## Bitwise Assignment Operators

| Operator | Example | Description |
|-----------|----------|--------------|
| `<<=` | `x <<= n` | Shift left by `n` |
| `>>=` | `x >>= n` | Shift right by `n` |
| `&=` | `x &= y` | Bitwise AND assignment |
| `|=` | `x |= y` | Bitwise OR assignment |
| `^=` | `x ^= y` | Bitwise XOR assignment |

These modify the **left operand** directly.

---

## Bit Masks

- A **bit mask** is a predefined set of bits used to **select** which bits will be modified in an operation.  
- Bits set to 1 indicate the bits we want to modify.

```cpp
constexpr std::uint8_t mask0{ 0b0000'0001 }; // represents bit 0
constexpr std::uint8_t mask1{ 0b0000'0010 }; // represents bit 1
constexpr std::uint8_t mask2{ 0b0000'0100 }; // represents bit 2
constexpr std::uint8_t mask3{ 0b0000'1000 }; // represents bit 3
constexpr std::uint8_t mask4{ 0b0001'0000 }; // represents bit 4
constexpr std::uint8_t mask5{ 0b0010'0000 }; // represents bit 5
constexpr std::uint8_t mask6{ 0b0100'0000 }; // represents bit 6
constexpr std::uint8_t mask7{ 0b1000'0000 }; // represents bit 7
```

### Example: Testing a Bit

You can test if a specific bit is on or off using a mask:

```cpp
std::uint8_t flags{ 0b0000'0101 };
static_cast<bool>(flags & mask3) // the bit in mask3 is set to 1, thus output is only dependent on the state of the 3rd bit in the flag
```
- Similar logic applies for flipping, resetting, or testing bits.

---

## Notes on Bit Flags

- Bit flags do **not always save memory** for small numbers of flags (since each mask and flag still take 1 byte).  
- For **large numbers of flags**, bit-level storage becomes more memory-efficient.  
- They also improve **clarity** when working with many boolean variables.  
- Bit masks can operate on **multiple bits** at once.

---

## How Values Are Stored in Memory

### Unsigned Integers

- Stored as straightforward binary representations of positive values.

### Signed Integers

- The **leftmost bit** is the **sign bit** (1 = negative, 0 = positive).  
- Negative numbers are stored using **two’s complement representation**:  
  1. Take the binary of the absolute value.  
  2. Invert all bits.  
  3. Add 1.

- Example storage for double types is more complex (see Chapter 4 notes).

---

## Interpreting Types and Symbol Table

- The compiler maintains a **symbol table**, a data structure storing information about identifiers (variables, functions, objects, etc.).  
- It tracks:  
  - Identifier type  
  - Scope  
  - Memory location  

- When accessing or storing a variable, the compiler uses this symbol table to interpret how values should be encoded and decoded in memory.  
- This is crucial because types determine **how bits are interpreted** — e.g., signed vs. unsigned integers, strings vs. floating-point values.
