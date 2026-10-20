---
tags:
  - cs136
  - c-programming
  - uwaterloo
course: CS 136
term: Fall 2026
---

# CS 136 — Elementary Algorithm Design & Data Abstraction — Complete Notes

*UWaterloo, Fall 2026. Moving from Racket (CS 135) into **C**. All lecture sections in one file. Code is in `c` fences; a per-chapter **Glossary** is at the end.*

## 📖 Index

1. [[#Topic 1 — Functional C|Topic 1 — Functional C]] — history & C99, compilation & files, `main` & exit codes, `printf`/tracing, operators, octal trap, static typing, identifiers & style, declarations vs definitions, booleans, the design recipe & `assert`, control flow, recursion (simple/accumulative/mutual)
2. [[#Topic 2 — Imperative C|Topic 2 — Imperative C]] — functional vs imperative, side effects & `effects:`, `printf`/`scanf` I/O, variables/mutation/`const`, scope & purity, expression statements, testing (streams, harnesses, terminology)
3. [[#Topic 3 — The C Model|Topic 3 — The C Model]] — control-flow model, loops & `break`/`continue`, iteration↔recursion, memory (bits/`sizeof`/overflow), `char`/ASCII, `struct`, the five memory sections, **call stack & stack frames** (pass-by-value), and drawing a **memory snapshot**
4. [[#Topic 4 — Pointers|Topic 4 — Pointers]] — the address operator `&`, pointers & pointer types, `NULL`, `sizeof` a pointer, the dereference operator `*`, the arrow operator `->`, and **aliasing**
5. [[#Glossary|Glossary]] — key terms, grouped by chapter

### How to read these notes

`inline code` = literal C · **⚠️ `[!warning]` / `[!danger]`** = classic bugs & exam traps · **💡 `[!tip]`** = instructor intuition · **✅ `[!success]`** = the key rule to remember · **`[!example]`** = quiz notes · **`[!abstract]`** = section goal/objectives.

> [!example] Quiz tracker
> - **Quiz 1** — Section 1 (functional) material: short answer (fill-in-the-blank / multiple choice) + a simple-recursion coding question. Full documentation required (purpose, requirements + `assert`, effects).
> - **Quiz 2** (~2 weeks out) — expect a **stack-frame tracing** exercise: given code, draw the call stack / memory snapshot at a specified line (see §3.18 and the drawing rules in §3.21).

> [!tip] Navigation
> In Obsidian, use the **Outline** panel (or click an Index entry) to jump between topics; in a terminal, search (`/`) for "# Topic N" or "## Glossary".

**Source materials:** slide decks `F26_CS136_NN_<name>_notes.pdf`, lecture transcripts, **and the official edX course text (Sections 1–2)** — Topics 1 & 2 below integrate every detail from the course book. Optional textbook: *CP:AMA* (C Programming: A Modern Approach).

---

# Topic 1 — Functional C

> [!abstract] Goal & objectives
> **Primary goal:** write and test simple **functions** in C. This section is about **C syntax & terminology**, not new programming ideas. By the end you should be able to:
> - use the C syntax/terminology and the operators `%  ==  !=  >=  &&  ||`;
> - write a simple C function and **explain the significance of `main`**;
> - do basic tracing with **`trace_int`**; use **`assert`** for testing and to verify requirements;
> - provide the **required documentation** for C functions.
>
> *Optional readings: CP:AMA 2.2, 2.3, 2.7, 4.1, 5.1, 9.1 (text orders topics differently).*

## 1.1 A Brief History & Versions of C

- **C** was created by **Dennis Ritchie (1969–73)** to make the **Unix** OS more **portable**. Named "C" as the successor to "**B**" (a smaller **BCPL** — Basic Combined Programming Language).
- It was designed for **low-level access to memory** and to be **easily translated into machine code**. Enormous amounts of software — and parts of every major OS — are written in C.

> [!note] C standards — we use C99
> C89/90 → **C99 (1999 — used in this course)** → C11 (2011, extra features we don't need) → C18 (2018, bug fixes) → C23 (2024). Our environment compiles to the C99 standard.

> [!warning] Don't "jump ahead"
> Read assignments carefully — you may be **forbidden from using features not yet covered** (e.g. loops during Section 1).

## 1.2 Two Paradigms

- **Functional** (CS 135 / Racket): **pure functions** — only return values, the return value depends **only on the arguments**, no side effects, only constants.
- **Procedural / imperative** (CS 136 / C): **procedures** — sequences of **statements** that often have **side effects** (e.g. changing memory). Section 1 writes C but stays *mostly* functional; Section 2 adds side effects.

## 1.3 Compiled vs Interpreted; Files; the Entry Point

- **Racket is interpreted** — DrRacket's interpreter runs code top-to-bottom; the **entry point** is the top of the file; it can even partially run buggy code.
- **C is compiled** — a **compiler** translates human-readable `.c` into machine (binary) code, producing an **executable** *only if there are no errors*. A program with errors can **never** run.

**The four kinds of file in a C program:**

| Extension | File |
|---|---|
| `.c` | **implementation** file (your code) |
| `.h` | **header** file (declarations to share) |
| `.o` (sometimes `.ll`) | **compiled** object file (optional intermediate) |
| `a.out` | the **executable** (default name) |

> [!success] The entry point is `main`
> Unlike many interpreted languages, C does **not** start at the top of the file — execution begins in a special function named **`main`**, and every C program has **exactly one** `main`.

## 1.4 `main`, Exit Codes & No Top-Level Code

```c
#include <stdio.h>

int main(void) {
  printf("Hello World!\n");
  return 0;   // optional in main
}
```

- `main` has **no parameters** (technically optional ones) and an **`int` return type**.
- Its return value is the program's **exit / error code** (`errno`): **0 = success**. If you omit `return`, `main` returns 0 automatically. The constant **`EXIT_SUCCESS`** (= 0) is also common.

> [!warning] Never return non-zero from `main`
> Unless an assignment says otherwise, returning a non-zero value from `main` makes your **Marmoset** tests fail. (Whether to write `return 0;` at all is a style preference — it's optional.)

> [!warning] No top-level expressions
> In C, **code only runs inside functions**. Expressions written at the top level (outside any function) are **not allowed** — unlike DrRacket's interactions window.

## 1.5 `printf`, `#include` & Tracing

- `printf` is part of the C library; to use it we `#include <stdio.h>` (a **header file**), which copies its declarations into our program before compiling. The course header **`cs136.h`** bundles many common libraries — include it for now.
- Producing output is a **side effect** (covered fully in Topic 2).

> [!tip] Tracing tools — leave them in
> `trace_int` (and `trace_msg`, `trace_bool`, `trace_char`, `trace_ptr`, …) from `cs136.h`/`cs136-trace.h` display values for debugging. They print to a **separate stream (stderr)**, are **ignored by tests**, and are handy in office hours — no need to comment them out.

> [!info] Program vs function documentation
> Document a **program**'s purpose at the **top of the file** (not necessarily where `main` is). `main` itself needs no extra documentation.

## 1.6 Comments

```c
// one-line comment (to end of line)
/* multi-line comment
   — can comment out big blocks */
```

> [!warning] No nested block comments
> `/* this /* nested */ error */` — the first `*/` closes the comment, leaving `error */` as broken code.

## 1.7 Expressions & Operators

- C uses **infix** notation with normal arithmetic rules; use **parentheses** for order. (Racket's prefix `(+ 3 3)` needs no parentheses for precedence.)
- C has **40+ operators** (CP:AMA Appendix A); each is **left- or right-associative** to break ties. Most arithmetic operators are **left-associative**: `a / b / c` = `(a / b) / c` (start from the left).

> [!warning] No exponent operator; `^` is not power
> C has **no exponentiation operator**. The `^` symbol is **bitwise XOR**, not "to the power of" (bitwise ops are out of scope).

## 1.8 Integer Division `/` and Modulo `%`

- `/` on integers **truncates toward zero** (standardized in C99).
- `%` is the **remainder** after integer division; also **left-associative**. Identity: `i % j == i - (i/j)*j`.

| Expr | Result |
|---|---|
| `22 / 7` | `3` |
| `7 / 2` | `3` (fraction discarded) |
| `22 % 7` | `1` |
| `-22 / 7` | `-3` |
| `-22 % 7` | `-1` |

> [!warning] Avoid `%` with negatives
> `i % j` takes the **sign of `i`**, which is counter-intuitive — prefer non-negative integers.

## 1.9 Leading Zeros = Octal (classic trap)

> [!danger] A leading 0 changes the base!
> An integer literal with a **leading zero is octal (base 8)**; `0x` is **hex (base 16)**:
> - `017` ⟹ **15** (not 17!) · `010` ⟹ **8** · `0x10` ⟹ **16**
> - `trace_int(017)` prints `15`; `my_add(010, 010)` ⟹ `16`.
>
> Never write a leading zero unless you really mean octal.

## 1.10 The Static Type System

- C is **statically typed**: every type is known **before** the program runs, and an identifier's type **cannot change**. (Racket is dynamically typed.)
- Because types are fixed, **"type" runtime errors cannot happen**, and there are **no** `integer?` / `string?`-style checks. `my_add("hello", 3)` simply **won't compile** — whereas `(my-add "hello" 3)` is a Racket *runtime* error.

> [!warning] Always write the type
> Omitting a function's return type or a parameter type is bad style; C assumes **`int`** and may warn `type specifier missing, defaults to 'int'`.

## 1.11 Identifiers

- Must **start with a letter**; may contain **letters, digits, underscores**.
- Use **`underscore_style` (snake_case)** — **required** in this course (e.g. `hst_rate`, `trace_int`, `quick_sort`). (`camelCase` is a Java convention.)
- **Avoid a leading underscore** (`_name`) — it can clash with reserved identifiers and Marmoset tests.

## 1.12 Anatomy of a Function & Terminology

```c
// my_add(a, b) returns the sum of a and b
int my_add(int a, int b) {
  return a + b;
}
```

- Return type (`int`) precedes the name; **parameters** have types and are separated by commas; the body is in **braces `{}`**; a `return` is the keyword + an expression + a **semicolon**.
- Calling `my_add(2, 3)` passes the **arguments** 2 and 3 (which become `a`, `b`).

| Racket | C |
|---|---|
| **apply** a function | **call** a function |
| **consume** arguments | **pass** arguments |
| **produce** a value | **return** a value |

## 1.13 `void` Parameters & No Nested Functions

A function with **no parameters** writes `void` in its header; **call it with empty parentheses** (don't pass `void`):

```c
int my_num(void) { return 42; }   // my_num() ⟹ 42
```

> [!warning] Always write `void` for no parameters
> Omitting it (`int my_num()`) is legal but historically means "**unknown / arbitrary** number of parameters" — so always write `void` to clearly communicate (and enforce) "no parameters".

- **No nested functions:** you cannot define a function inside another (GCC has a non-standard extension, but it isn't C).

## 1.14 Declarations vs Definitions; Function Order

- An identifier must be **declared before it is used** (the compiler reads top-to-bottom). Calling a function the compiler hasn't seen gives: `implicit declaration of function '…' is invalid`.
- A **declaration** states only the *type*; a **definition** *creates* the thing (and also counts as a declaration). **Declared many times, defined once.**
- A **function declaration** (a **prototype**) is the header + `;`:

```c
int my_add(int a, int b);   // declaration / prototype — note the ';'
```

- C **ignores parameter names** in a declaration (only the types matter): `int my_add(int, int);` is equivalent — but include meaningful names for clarity.

## 1.15 Documentation, Whitespace & CS 136 Style

- **Every function needs a purpose**: an **example call** + a brief description of **what** it does (not *how*). Add a `// requires:` line when appropriate. **Don't** restate contract types — they're already in the code.
- C mostly **ignores whitespace**, but readable layout matters.

> [!note] CS 136 style highlights (follow the Style Guide)
> - Opening brace `{` at the **end of the line**; closing brace `}` **aligned** with the statement that opened it, on its **own line**.
> - Indent blocks **2 / 3 / 4 spaces**, consistently.
> - **Space after commas** and **around operators**; continue long lines **indented**. Markers may not follow poorly formatted code.
> - The chosen style is the **most widely accepted** for C/C++ (it conforms to the **Google style guide**); "best" style is partly taste, so just be **consistent**.

## 1.16 Numeric Type (for now: `int`)

For this section we use **`int`** (a 32-bit integer). The other types (`char`, `bool`) and the full ranges come in Topic 3.

## 1.17 Booleans, Comparison & Logical Operators

- C Boolean expressions produce **`1` (true)** or **`0` (false)** — not keywords. `true`/`false` are predefined as 1/0 for convenience.
- **Comparison:** `==` (equal), `!=` (not equal), `<`, `<=`, `>`, `>=`.
- **Logical:** `!` (not), `&&` (and), `||` (or), with **short-circuiting** — `(a != 0) && (b / a == 2)` won't divide when `a == 0`.

> [!danger] `==` vs `=`
> Equality is **`==`**; a single **`=`** is **assignment** — one of the most common and serious C bugs.

> [!warning] Single `&` / `|`
> Using one `&` or `|` instead of `&&` / `||` is a common mistake (those are bitwise operators).

> [!success] Truthiness — only 0 is false
> Operators that *produce* a Boolean give 0/1, but operators that *expect* one treat **any non-zero value as true**; **only 0 is false** (and `NULL`, Topic 3). So `2 && 3 ⟹ 1`, `0 || -2 ⟹ 1`, `!5 ⟹ 0`.

> [!note] Order of operations (don't memorize — add parentheses)
> `!`  →  `* / %`  →  `+ -`  →  `< <= >= >`  →  `== !=`  →  `&&`  →  `||`.

**`bool` type:** an integer restricted to 0/1 (e.g. functions like `is_even`, `my_negate`).

## 1.18 The Design Recipe & `assert`

```c
// my_div(x, y) returns the integer quotient x / y
// requires: y is not 0
int my_div(int x, int y) {
  assert(y != 0);
  return x / y;
}
```

- Purpose statement + `// requires:` + **`assert`** the requirement. The **contract lives in the types** (no contract comment).

> [!note] `assert(exp)`
> If `exp` is **false (0)** it prints a message and **halts** the program; if **true (non-zero)** it does nothing and continues. It's C's analogue of Racket's `check-expect`, used both to **test** (in `main`) and to **verify requirements**. (When all asserts pass, you see **no output**.)

> [!tip] Asserting requirements
> - `assert` every **feasible** requirement.
> - **Infeasible** ones are documented, not asserted: `// requires: n is a prime number [not asserted]`.
> - With multiple requirements, use **several small asserts** — so you can tell exactly which one failed.

## 1.19 Testing & Debugging (exam-relevant habits)

- **Write tests first** (before code); start simple → corner cases; be an **adversary** trying to break the function.
- Prefer **small, well-named helper functions** — easier to read, check, test, and debug.
- Debugging method: **understand** the bug before changing anything; **fix one bug at a time**; find a **minimal failing test**; form **hypotheses** and rule them out one by one; **check your tests too** (sometimes the test is wrong, not the code).
- Classic mistakes: `=` vs `==`; `<=` vs `<`; `>=` vs `>`; `|` vs `||`; `&` vs `&&`; **off-by-one**; and *buggy tests* (the hardest bugs "don't exist" — check your assertions too).

> [!tip] Pro tip — keep a bug log
> Record your bugs and their fixes (notebook or file). Reviewing them helps you avoid repeating the same mistake later.

## 1.20 Control Flow

`return` is a **control-flow** statement (ends the function, returns to the caller). The four kinds of control flow: **compound statements (blocks)**, **function calls**, **conditionals (`if`)**, and **iteration (loops — Topic 3)**.

- **Compound statement `{}`**: statements run **in sequence** until the block ends or a `return` executes. Code after a `return` **never runs**.
- **Function call**: execution **jumps** to the function; `return` **jumps back** to the caller (C tracks where — the call stack, Topic 3).

### `if` / `else` / `else if`

```c
if (expression) statement        // runs statement only if expression is non-zero
```

- Only the **next statement** is conditional — use a **block** for several (and, as good style, **always** use braces).
- `if`/`else` for two cases; an `if` that `return`s may not need `else`; **braces prevent a "dangling else"**; `else if` for 3+ cases.

> [!warning] `if` does not produce a value
> Unlike Racket's `cond`, C's `if` only controls flow — it **can't** be used inside an expression.

**Ternary `?:`** *does* produce a value: `q ? a : b` is `a` if `q` is non-zero, else `b`.

```c
(v >= 0) ? v : -v    // abs(v)
(a > b) ? a : b      // max(a, b)
```

Use it **sparingly** (overuse hurts readability).

> [!warning] Don't use `switch` or `goto`
> `switch` has error-prone **fall-through** (multiple branches can run); `goto` makes "spaghetti code". Both are **banned in this course**.

## 1.21 Recursion

**Simple recursion** (same idea as Racket, different syntax): the `if` true-branch is the **base case**, the false-branch the **recursive case**. It leaves **work to do on the way back** (e.g. "remember to add `n`"), so it can use **more memory** (see the call stack, Topic 3).

```c
int sum_to(int n) {
  if (0 == n) {
    return 0;
  } else {
    return n + sum_to(n - 1);   // work remains after the call returns
  }
}
```

A function may have **multiple `return`s**, but only **one value** is ever returned. Test with `assert(sum_to(5) == 15);`.

**Accumulative recursion**: a **wrapper** calls a **helper** with an extra **accumulator** parameter that does the work as it goes (no pile of deferred work):

```c
int sum_to_acc(int n, int acc) {
  if (0 == n) return acc;
  return sum_to_acc(n - 1, n + acc);
}
int sum_to(int n) { return sum_to_acc(n, 0); }   // wrapper
```

**Mutual recursion**: two functions call each other (e.g. `is_even`/`is_odd`). This **requires a forward declaration** of whichever function is defined later.

```c
bool is_odd(int n);   // forward declaration — without it, is_even can't call is_odd
bool is_even(int n) { return n == 0 ? true  : is_odd(n - 1); }
bool is_odd(int n)  { return n == 0 ? false : is_even(n - 1); }
```

---

# Topic 2 — Imperative C

> [!abstract] Goal & objectives
> **Primary goal:** write programs that use **I/O and mutation**. By the end you should be able to:
> - explain what a **side effect** is and document it with an **`effects:`** section;
> - print with **`printf`** and read with **`scanf`**;
> - define **global/local** mutable variables and **constants**; use the C **assignment operators**;
> - use the new terminology: **mutation**, **expression statements**, **compound statements `{}`**.
>
> *Optional readings: CP:AMA 2.4, 3.1, 4.2–4.5, 5.2, 10.*

## 2.1 Functional vs Imperative

- **Functional**: pure functions (only return values; return depends only on args); only constants.
- **Imperative**: functions may be **impure**; uses **variables and constants**; executes a **sequence of statements**; **uses side effects** — the single most important difference from functional programming. A **block `{}`** is a **compound statement**: a sequence of statements run in order (it may also hold local variable definitions).

## 2.2 I/O Overview

**I/O** = how a program interacts with the real world. Input: keyboard/touch/voice/camera; output: screen/sound/vibration; also files, printers, GPS, network. **This course uses simple text I/O only.**

## 2.3 Output with `printf`

```c
printf("One thing is %d and the other is %d.\n", 2 + 3, 84 / 2);
// One thing is 5 and the other is 42.
```

- The first argument is always a **string**; `\n` is the **newline**.
- A **format specifier `%d`** = a decimal integer, replaced by the matching extra argument; you may have **several** (one argument each).
- Escapes inside the string: **`%%`** prints `%`, **`\\`** prints `\`, **`\"`** prints `"`.
- `printf`'s full format syntax can also control **field width and alignment** (CP:AMA 22.3), but **simple `%d` is usually sufficient** in this course.

> [!warning] `printf` is NOT a `void` function
> It **returns an `int`** = the **number of characters printed**. `printf("hello!\n")` returns **7** (5 letters + `!` + `\n`, since `\n` is one character). That returned value is simply discarded (see expression statements, §2.6).

## 2.4 Side Effects & State

- A **side effect** is when the **state** of something **changes** (state = the value of data at a moment in time). Writing your name on a blank page changes its state — a side effect.
- Compare `sqr` (just returns a value) with `noisy_sqr` (returns **and** prints) — the latter has a side effect.

> [!success] Document side effects with `effects:`
> Any function that prints gets `// effects: produces output`. If the effect is **conditional**, use **"may"** (`// effects: may produce output`). No further detail is needed.

> [!warning] I/O terminology (markers care about this)
> Don't say "input 7 into `sqr`, it outputs 49" — `sqr` neither reads input nor prints. Say "**if 7 is passed to `sqr`, it returns 49**." For `noisy_sqr`: "it **outputs** a message **and returns** 49." Never confuse **output** (via `printf`) with the **return value**.

> [!note] Debugging tools aren't side effects
> `assert` and `trace_int` are **not** documented in `effects:`. (Large projects keep thousands of them, disabled in production builds.)

## 2.5 `void` Functions

A function may have a side effect and return **nothing** → return type **`void`**. The `return` is then **optional** and takes **no expression** (a `void` function returns automatically at the end).

```c
// display_score(score, max) prints the score out of max
// effects: produces output
// requires: 0 <= score <= max
void display_score(const int score, const int max) {
  assert(score >= 0);
  assert(max >= 0);
  assert(score <= max);
  printf("Your score is %d out of %d.\n", score, max);
  return;   // optional
}
```

Mathematically this is a **procedure** (a function must always return a value), but C calls everything a "function".

## 2.6 Expression Statements & the Three Statement Types

- An **expression statement** is an expression + **`;`**. Its **value is discarded** after it runs — its purpose is the **side effect**. Imperative programming is, in essence, "**programming by side effects**."
- edX warns on an expression statement that **obviously** has no side effect (`8 + 1;`) but **not** when it contains a function call (side effects are assumed).

> [!note] The three kinds of statement
> 1. **Compound statements (blocks) `{}`** — a sequence run in order.
> 2. **Expression statements** — for side effects (value discarded).
> 3. **Control-flow statements** — `return`, `if`, `else`, loops, …
>
> A variable **definition** is **not** a statement — it's a separate syntactic unit.

## 2.7 Variables, Definitions & Initialization

To **define** a variable: **type**, **identifier**, (initial value), `=`, `;`.

```c
int m = 5;   // definition WITH initialization (good style)
```

- A variable must **always have a value**. You *can* define without initializing, but it's **bad style** — **always initialize**. (Uninitialized behaviour: Topic 3.)
- **Initialization ≠ assignment:** both use `=` but have different semantics; the `=` in initialization is **not** the assignment operator (the distinction matters more in C++). You *can* define several at once (`int a, b = 5;`) but it's **bad style** — and there `a` is **uninitialized**.

## 2.8 Mutation & the Assignment Operator

- **Mutation** = changing a variable's value (a **side effect**). Bank-balance analogy: a withdrawal changes the balance's state.
- The **assignment operator `=`**: evaluate the **RHS** (same type as the LHS), then store it in the LHS **variable**.

```c
m = m + 1;   // mutate m
m += 1;      // same, compound operator
```

> [!warning] `=` is not symmetric
> `x = y;` is **not** the same as `y = x;`. (Some languages write `:=` or `<-` to make this obvious.)

> [!tip] `=` also produces a value
> Assignment yields the RHS value, which enables `x = y = z = 0;` ≡ `(x = (y = (z = 0)))`. **Avoid** this — keep **one side effect per expression statement**; mutate each variable on its own line.

> [!danger] `=` vs `==` (again)
> `if (i = 13)` **assigns** 13 to `i` and is therefore always true. **Defensive style:** put the constant on the left — `if (13 == i)` — so a slipped `=` (`13 = i`) **fails to compile**.

## 2.9 Compound Assignment & Increment / Decrement

- Compound operators: `+=`, `-=`, `*=`, `/=`, `%=`.
- Increment / decrement: `++` / `--`, in **prefix** or **postfix** position.

> [!note] Prefix `++x` vs postfix `x++`
> - `x++` yields the **old** value, **then** increments.
> - `++x` increments, **then** yields the **new** value.
> - With "one side effect per statement" it doesn't matter which you use, but **prefix is preferred** (clarity/efficiency). Example: `int y0 = x++;` → `y0` is the *old* `x`; `int y1 = ++x;` → `y1` is the *new* `x`.
>
> (Fun fact: the language **C++** is a pun — "one better than C".)

## 2.10 Constants (`const`)

```c
const int rate = 13;   // immutable; mutating it is a compile-time error
```

Good style — `const` **communicates intent**, **prevents accidental mutation**, and **may help the optimizer**. ("Variable" covers both kinds; we say "mutable variable" vs "constant" when the difference matters.)

## 2.11 Scope: Global, Local, Shadowing

- **Scope** = the region of code where an identifier is visible. **Global** variables (defined at the top level) are in scope **anywhere below** their definition; **local** variables have **block scope** — from their definition to the end of the enclosing `{}` block.
- **Shadowing:** an inner variable with the same name **hides** an outer one (poor style).
- **C99** lets you define a local **anywhere** in a block (old C required all locals at the top). Guideline: declare in the **narrowest scope** possible, **as close to first use** as possible.

## 2.12 Pure vs Impure Functions; Globals Banned

- A function is **impure** if it has a **side effect** *or* **depends on a global mutable** variable.
- **Mutating a global mutable variable** is a side effect — and is **not allowed** in this course (Assignment Rule 12.1) unless explicitly permitted.
- **Mutating a local variable or a parameter** is **not** a side effect (it only touches local state) — such functions remain **pure**. (Parameters behave like locals; mutating them is modelled later.)
- **Global dependency:** merely *reading* a global *mutable* variable makes a function **impure even if it has no side effect**.

> [!success] Globals: mutable = bad, const = good
> **Global mutable variables** are almost always poor style and **banned** here. **Global constants** are **great style** and encouraged. **Static local variables** (local scope but global lifetime — their value **persists between calls**) are likewise **not allowed** in this course.

## 2.13 Input with `scanf`

```c
int val = 0;
int retval = scanf("%d", &val);   // read one int into val
```

- First arg: a **format specifier** (`"%d"` = decimal int). Second arg: **`&val`** — the **address** of the variable (the `&` is explained in Topic 4). On success, `val` is **mutated** to the value read.
- **Read only ONE value per `scanf`** (eases debugging and testing).

> [!success] Always check `scanf`'s return value
> `scanf` returns the **count of values successfully read**, or **`EOF`** at end of input; **invalid** input (e.g. `"%d"` against `hello`) returns **0**. For a single read, **1 = success**. (`EOF` is `-1` in our environment, but **write `EOF`**, not `-1`.)

> [!note] One `scanf` call, three effects
> `retval = scanf("%d", &val);` causes (1) **input is read** (a side effect), (2) **`val` is mutated**, (3) **`retval` is mutated**.

**Reading many values (recursion).** Forward vs reverse simply swap the print and the recursive call:

```c
// print_forward() echoes all ints from input, in order
// effects: reads input; produces output
void print_forward(void) {
  int n = 0;
  int retval = scanf("%d", &n);
  if (retval == 1) {          // success
    printf("%d\n", n);
    print_forward();          // put the recursive call BEFORE the print for reverse order
  }
  // read failed (bad input or EOF) → base case, just return
}
```

> [!warning] Whitespace & invalid input
> `scanf("%d", …)` **skips whitespace** (spaces and newlines) — spaced and newline-separated input look identical to it. On malformed input like `4 23skidoo 57`: it reads `4` (→1), reads `23` (→1), then **fails** — it will **not** skip over `skidoo` to reach `57`. (This course usually won't test invalid input.)

## 2.14 Testing: `[Run Code]` vs `[Run With Tests]`, Streams, Harnesses

- **`[Run Code]`** — input from the **Stdin** box; output to **Code Output**.
- **`[Run With Tests]`** — input from **`.in`** files; output compared against **`.expect`** files (you often need a trailing newline in `.expect`). You can add **multiple** `.in`/`.expect` pairs.
- **Streams:** `printf` → **stdout**; tracing → **stderr**. Marmoset and `[Run With Tests]` compare **stdout only**; `[Run Code]` may show the two streams **mixed**.

> [!note] Testing strategies
> - **Return values:** `assert` (e.g. in `main`).
> - **Input/output:** `[Run With Tests]` with `.in` / `.expect`.
> - **Testing harness:** a function that **reads argument values from input, calls the function under test, and prints the results** — works for pure *and* impure functions, and lets non-coders write tests. A fancier harness can pick which function to run from a **symbol** (`lookup_symbol` / `read_symbol` in `cs136.h`, e.g. `ABS` / `SQR` / `SQUARE`).

> [!note] Testing terminology (exam vocab)
> **Assertion testing** (asserts) · **I/O-driven testing** (`.in`/`.expect`) · **testing harness** (input drives function calls) · **white-box** (you can see the code) vs **black-box** (you cannot) · **unit testing** (one piece at a time) · **regression testing** (re-run *all* tests after any change, to catch newly broken code — "regressed").

## 2.15 Worked Recursion Examples (from lecture)

*(These complement the textbook's `print_forward` / `count_even_inputs`.)*

**`twice_all`** — read ints, print each doubled (input `2 4 6 0 1` → `4 8 12 0 2`):

```c
// effects: reads input; produces output
void twice_all(void) {
  int val = 0;
  if (1 == scanf("%d", &val)) {
    printf("%d\n", 2 * val);
    twice_all();
  }
}
```

**`countdown` / `countdown_all`** — a test harness driving `countdown`:

```c
// countdown(n) prints n .. 1
// requires: n >= 0
void countdown(const int n) {
  assert(n >= 0);
  if (n != 0) { printf("%d\n", n); countdown(n - 1); }
}
// countdown_all() runs countdown on each int read
// effects: reads input; produces output
void countdown_all(void) {
  int v = 0;
  if (1 == scanf("%d", &v)) { countdown(v); countdown_all(); }
}
```

**`partsum`** — fixed prototype `int partsum(void)`; add a **helper** for the accumulator (input `2 4 6 0 1` → `2 6 12 12 13`):

```c
int partsum_helper(int acc) {
  int v = 0;
  if (1 != scanf("%d", &v)) return acc;   // base case: no more input
  acc += v;
  printf("%d\n", acc);
  return partsum_helper(acc);
}
int partsum(void) { return partsum_helper(0); }
```

**`downup`** — print `n`..`0`..`n` with **no helper and no extra parameter** (wishful thinking: print `n` **before and after** the recursive call; `downup(2)` → `2 1 0 1 2`):

```c
// requires: n >= 0
void downup(const int n) {
  assert(n >= 0);
  printf("%d\n", n);
  if (n > 0) { downup(n - 1); printf("%d\n", n); }
}
```

**`count_even_inputs`** (textbook) — **return** how many of the ints read are even (a *reading + counting* recursion; base case returns 0):

```c
// count_even_inputs() returns how many even ints are read from input
// effects: reads input
int count_even_inputs(void) {
  int n = 0;
  if (1 != scanf("%d", &n)) return 0;                 // base case: no more input
  return (n % 2 == 0 ? 1 : 0) + count_even_inputs();  // count this one, then recurse
}
```

> [!example] Quiz note
> The first written quiz covers **Section 1** (functional) material — short answer + a simple-recursion coding question. Provide full documentation: **purpose**, **requirements** (with `assert`), and going forward **effects**. Full `scanf` details aren't needed for that first quiz.

---

# Topic 3 — The C Model

> [!abstract] Goal & objectives
> **Primary goal:** model **how C programs execute**. By the end you should be able to:
> - use the control-flow statements `return`, `if`, `while`, `do`, `for`, `break`, `continue`;
> - **rewrite a recursive function as iteration and vice versa**;
> - explain **integer limits & why overflow occurs**; use **`char`** and **ASCII**; use **`struct`**s;
> - explain C's **memory + control-flow** model (vs Racket's substitution); name the **4 memory areas so far** (code, read-only data, global data, stack) and say **which section an identifier lives in**;
> - explain a **stack frame** and its components (**return address, parameters, local variables**) and how C **copies arguments** (pass by value);
> - **model a small program by hand** and **draw the memory snapshot** (incl. stack frames) at a given execution point.
>
> *Optional readings: CP:AMA 6.1–6.4, 7.1–7.3, 7.6, Appendix E.*

## 3.1 Two Models: Control Flow + Memory

Racket modelled computation with **substitution** ("stepping rules"): evaluate all arguments to **values**, then **apply** a function by substituting its body with the parameters replaced by those argument values.

```
(define (my-sqr x) (* x x))
(+ 2 (my-sqr (+ 3 1))) ⟹ (+ 2 (my-sqr 4)) ⟹ (+ 2 (* 4 4)) ⟹ (+ 2 16) ⟹ 18
```

> [!success] C's model = control flow + memory
> We model C with **two complementary mechanisms**: **control flow** (which statement runs next) and **memory** (what is stored where). Racket's substitution model **does not** work for C.

- **Control flow** tracks the **program location** — "where" execution currently is. It **starts at the beginning of `main`**. *(In hardware this is the **program counter**, the address of the current machine instruction — CS 241.)*
- Four kinds of control flow (first three from Topic 1): **compound statements (blocks)**, **function calls**, **conditionals (`if`)**, and now **iteration (loops)**.

## 3.2 Tracing C

C is **compiled**; the program that runs is the compiled (machine-code) version, with an **almost one-to-one** correspondence to C statements — so we trace **"line by line"**. `main` runs statements one at a time; a call **jumps** into the function; a `return` **jumps back** to the caller. **Control-flow statements** disrupt this sequential order.

## 3.3 `if`/`else`

> [!success] if-Rule
> Evaluate `expression`; if true execute `statement0`, otherwise execute `statement1` (when present).

```c
void describe(int n) {
  if (0 == n % 2) {
    printf("%d is even\n", n);
  } else {
    printf("%d is odd\n", n);
  }
  if (0 == n % 10) {
    printf("%d ends with 0\n", n);
  }
}
```

Nested `if`/`else` and `else if` chains express multi-way choices (equivalent forms).

## 3.4 `while` Loops

> [!success] while-Rule
> `while (expression) statement` — (1) evaluate `expression`; (2) if true, execute `statement`, then go to step 1. (Like `if`, but it **loops back**. Always use a **block** `{}`.)

```c
int n = 5;
while (n > 0) {
  printf("%d\n", n);
  n = n - 1;
}
```

> [!warning] Exam point — the body may not run
> The loop condition is checked **before** the body runs, and re-checked each pass. So if the condition is false the *first* time, **the body may not execute at all**. A quiz asked *when* the condition is evaluated; the key idea is that execution of the body is **not guaranteed**.

> [!tip] Looping is inherently imperative
> A `while` loop relies on **mutation**: if nothing in the body changes the condition, it never becomes false → an **endless (infinite) loop**. Loops can also be **nested** inside one another.

*Exercises (no recursion):* `int factorial(int n)` (`factorial(5) ⟹ 120`) and `int sum_squares(int n)` (`sum_squares(4) ⟹ 4*4+3*3+2*2+1*1 = 30`).

## 3.5 Iteration vs Recursion (convert both ways)

> [!success] Iteration ↔ recursion
> Using a loop to repeat work is **iteration** — the imperative alternative to **recursion** (which repeats via **function calls**). Any loop can be written as recursion and vice versa.

- They have **similar efficiency**, but iteration is often better than **simple** recursion (it creates **no growing stack of frames** — see §3.20); sometimes iteration is easier to read, sometimes recursion is more intuitive (e.g. inorder tree traversal, later).
- **Accumulative recursion maps almost directly to a `while` loop** — the accumulator parameter becomes a mutable variable updated each pass.

```c
// accumulative recursion …
int sum_to_acc(int n, int acc) {
  if (n == 0) return acc;
  return sum_to_acc(n - 1, acc + n);
}
int sum_to(int n) { return sum_to_acc(n, 0); }
```

```c
// … becomes this iteration
int sum_to(int n) {
  int acc = 0;          // the accumulator is now a mutable variable
  while (n > 0) {
    acc += n;           // same update the recursive call did in its argument
    --n;
  }
  return acc;
}
```

## 3.6 `do`/`while` Loops

Execute the body **once before** the first condition check.

> [!success] do-while-Rule
> (1) execute `statement`; (2) evaluate `expression`; (3) if true, go to step 1.

```c
do {
  // ... body runs at least once ...
} while (expression);
```

> [!tip] When "run at least once" helps — input validation
> You want to *get* the input before you can *check* it ("you can't check the input is correct if you don't have it yet"). So: the body reads the input, then the condition validates it and loops again if it's wrong. Most of the time, though, you guard with a `while` and want the option to run **zero** times. ("I can't think of the last time I used a `do`/`while`.")

## 3.7 `break` and `continue`

- `break` — immediately **terminates the innermost loop**, then continues the rest of the function.
- `continue` — **skips the rest of the statements in the current block** and continues the loop (jumps back to the top / the next iteration).

> [!warning] `break` only exits loops
> `break` terminates a **loop** only — you **cannot `break` out of an `if`**. To leave a function entirely, use `return`.

```c
int n = 10;
while (true) {           // break example
  --n;
  printf("I see %d\n", n);
  if (5 == n) {
    break;               // leaves the loop; the printf below still runs
  }
}
printf("n must be 5: %d\n", n);

int m = 10;
while (m > 0) {          // continue example
  --m;
  if (!(m % 3)) {        // m divisible by 3  →  (m % 3)==0 is false, !false is true
    printf("Skipping %d\n", m);
    continue;            // skip the rest of this iteration
  }
  printf("Including %d\n", m);
}
```

> [!tip] break vs return
> `break` is "like `return` but weaker": `return` inside a loop ends the **whole function**, whereas `break` ends **only the loop** and execution continues afterward. Both can exit a loop; pick based on whether you still have work to do in the function.

> [!tip] Infinite loops are sometimes intentional
> `while (true)` looks infinite, but `break` (or `return`) can exit it. A **daemon / background process / web server** deliberately loops "forever", waiting for requests — it's not truly infinite because it can be broken out of.

> [!success] continue is optional
> You can always rewrite code to avoid `continue`. It's a convenience that can make some code cleaner; the skip-the-multiples-of-3 loop can instead wrap the "included" work in an `if`/`else`. *(Style note: `!(m % 3)` relies on `0` being false; clearer to write `0 == m % 3`.)*

## 3.8 `for` Loops

Many languages have a counting loop (Fortran `do`, BASIC `for…next`, Pascal, Python `range`). In C:

```c
for (int i = 1; i <= 10; ++i) {
  printf("%d\n", i);
}
```

> [!tip] Which loop when?
> Use a **`while`** when you *don't know* how many iterations you'll need; use a **`for`** when you *do* (counting a known number of times). `for` loops are very common.

A `for` loop is shorthand for a `while`:

```c
for (setup_statement; expression; update_statement) {
  body_statements;
}
```

> [!success] for-Rule
> (1) execute `setup_statement` (usually initialize a loop counter); (2) evaluate `expression` (loop while true); (3) if true, execute `body_statements`; (4) execute `update_statement` (increment/decrement the counter), then go to step 2.

> [!warning] THE for-vs-while distinction — `continue` + the update
> `for` and `while` are "almost identical"; the difference shows up with `continue`:
> - In a **`for`** loop, `continue` still runs the `update_statement` before the next check.
> - In a hand-written **`while`** equivalent, `continue` skips **everything** left in the body — **including a manual update at the bottom** — so the counter never changes → **infinite loop**.
>
> When converting a `for` to a `while`, place the update so `continue` can't skip it.

> [!note] Common `for` idioms (watch off-by-one: `<` vs `<=`)
> - up `0 … n-1`: `for (int i = 0; i < n; ++i)`
> - up `1 … n`: `for (int i = 1; i <= n; ++i)`
> - down `n-1 … 0`: `for (int i = n - 1; i >= 0; --i)`
> - down `n … 1`: `for (int i = n; i >= 1; --i)`

> [!tip] More `for` facts
> - **C99:** the setup may be a **definition**, giving a variable with **block scope** limited to the loop (`for (int i = …)`).
> - **Any** of the three parts may be **omitted**; an omitted **expression** is always **true** (`for (;;)` loops forever).
> - The **comma operator** allows several sub-expressions (`for (i = 1, j = 100; i < j; ++i, --j)`) — **don't use it in this course**.

## 3.9 Tracing Tools for Debugging

> [!tip] Trace, don't `printf`, to debug
> The tracing tools (`trace_int`, …) let you **see the control flow** and don't interfere with I/O testing (they write to **stderr**) — leave them in. On assignments **never `printf` unnecessary output** (it can break correctness tests); use tracing instead.

## 3.10 Memory: Bits, Bytes & Addresses

- A **bit** is 0/1; a **byte** is **8 bits** (256 possible states). The **byte** is the smallest addressable unit, and its position is its **address**. With 1 MB of RAM, addresses run `0 … 1 048 575` (= 2²⁰ − 1), i.e. **`0x0 … 0xFFFFF`** in hex. Picture memory as labelled **mailboxes**, one byte each.

> [!success] A variable is a name for a *location*
> In Racket's substitution model a variable is a **name for a value**. In C's memory model a variable is a **name for a location (address)**: using it **fetches** the contents stored at that address.

**Defining a variable** (`int n = 0;`) makes C (1) **reserve** space in memory, (2) **track** that address, (3) **store** the initial value there. We draw memory with hex addresses, decimal values, in 4-byte rows:

| address | data (4 bytes) |
|---|---|
| `0x00` | 136 |
| `0x04` | -42 |
| … | … |
| `0xFC` | 24765 |

## 3.11 `sizeof` & Integer Limits

> [!note] `sizeof` is an operator
> `sizeof` gives the **number of bytes** a type (or identifier) needs. It *looks* like a function but is an **operator**. In this course **`sizeof(int)` is 4 bytes (32 bits)**, so C reserves 4 **consecutive** bytes and tracks the **starting address**. *(The real size depends on the machine's word size; `inttypes.h` provides exact-width types like `int32_t` / `int64_t`. We always use `int` = 32 bits.)*

- A 32-bit `int` has **2³² = 4 294 967 296** possible values → range **−2³¹ … 2³¹−1** (`INT_MIN` / `INT_MAX`).
- **`unsigned int`** covers `0 … 2³²−1` (not used here). `sizeof` actually returns a **`size_t`** (an unsigned int — sizes are never negative), but we use `int` for sizes.

## 3.12 Overflow

> [!warning] Overflow is undefined
> Representing a value outside `INT_MIN … INT_MAX` causes **overflow**, and the result is **undefined** — **never assume** what you'll get (it may look random, roll over like `2147483647 + 1 ⟹ -2147483648`, or the environment may **catch it and error** — edX does, with the guardrail disabled only for demos). Careful **ordering of operations** can sometimes avoid it.

> [!tip] Why Racket never overflowed
> Racket stores numbers in a **structure that uses as many bytes as needed** (so `(expt 2 1000)` is fine). C uses a **fixed** number of bytes; a big-integer library (e.g. **gmplib.org**) gives C the same ability. *(More in CS 251 / CS 230.)*

## 3.13 The `char` Type & ASCII

A `char` stores an integer in **1 byte** (256 values, **−128 … 127**) — rarely used for calculation, mainly for **characters**.

**ASCII** (American Standard Code for Information Interchange) numbers each character: **`'A'` = 65**, **`'a'` = 97**, **`'z'` = 122**, **space = 32**; codes **0 … 31** are teletype **control characters** (e.g. 7 = bell); the only one we use is **line feed = 10 = `\n`**. *(Modern text uses **Unicode** — 100 000+ characters — often via **UTF-8** (ASCII in 1 byte, others more); not used here.)*

- In C, **single quotes** denote an ASCII char: `'a'` **is** 97. Print a char with **`%c`** (or `%d` for its number).

> [!danger] `'a'` vs `"a"`
> Always use **single quotes** for a character. `"a"` (double quotes) is a **string**, not the char `'a'`.

```c
char ch65 = 65;     char ch_A = 'A';                 // identical
assert(ch65 == ch_A);                                // 65 == 'A'
char nl = '\n';     assert(nl == 10);                // '\n' == 10
```

**Character arithmetic** (chars are integers → avoids magic numbers):

```c
bool is_lowercase(char c) { return 'a' <= c && c <= 'z'; }
char to_lowercase(char c) { return is_uppercase(c) ? c + ('a' - 'A') : c; }
```

## 3.14 Reading Characters

Read a char with **`scanf("%c", &c)`** — remember **every keystroke is a character**, including space, newline, `$`, `%`, etc.

> [!warning] Whitespace when reading chars
> `scanf("%d", …)` always **skips leading whitespace**. `scanf("%c", …)` does **not** — it reads the very next character (possibly a space or newline). Use **`" %c"`** (a **leading space** in the format string) to **skip whitespace first**. So a `count_char` using `"%c"` counts whitespace, while one using `" %c"` does not.

```c
char c = 0;
if (1 == scanf(" %c", &c)) {            // leading space skips whitespace
  printf("Read '%c' (%d)\n", c, c);
} else {
  printf("Failed to read a character.\n");
}
```

## 3.15 Symbols, Floats & Doubles (brief)

> [!note] Not core, but in the readings
> - **Symbols:** C has **no** Racket-style `symbol` type. A C "symbol" is just a **constant** (often an `int`) with a meaningful **`ALL_CAPS`** name but an arbitrary value — used to avoid magic numbers. (`enum`, CP:AMA 16.5, formalizes this; not taught here.)
> - **`float` / `double`:** real-number types (print with **`%f`**), like Racket's **inexact** numbers — subject to **precision error**. `double` is more precise because it uses more memory (float = 24-bit mantissa + 8-bit exponent; double = 53 + 11). Test like `check-within`. **Don't use `float`/`double` unless told to.**

## 3.16 Structures (`struct`)

A **structure** joins multiple pieces of data into one value (like Racket's `define-struct`).

```c
struct posn {      // 'struct' keyword, braces, trailing semicolon; field types required
  int x;
  int y;
};                 // ← don't forget this semicolon

struct posn p = {3, 5};              // positional; the type is "struct posn", not "posn"
struct posn q = { .y = 4, .x = 3 };  // C99 designated init (any order)
struct posn r = { .x = 3 };          // omitted fields default to 0 → r.y == 0
printf("%d %d\n", p.x, p.y);         // dot operator selects a field (no selector fns)
p.x += 4;                            // mutate one field
```

> [!warning] Structs: `=` copies, but `{}` is init-only
> - `=` **copies all fields** (`q = p;` works); you can also mutate individual fields.
> - `{…}` braces are **initialization syntax only** — `p = {5, 6};` is **invalid**; mutate fields, or assign a freshly-built struct.
> - **`==` does not work** on structs → write your own equality function. **`printf` only takes elementary types** → print each field.

**In memory:** a struct *definition* reserves **no** memory — space is reserved only when a **variable** is defined. `sizeof(struct posn)` is **at least the sum** of its fields' sizes but **may be larger** (C adds **padding** for alignment/efficiency, so size can depend on **field order**). **Always use `sizeof`** to get the size.

| id | type | addr | data (4 bytes) |
|---|---|---|---|
| p | struct posn | `0xF0` | 3 → (after `p.x += 4`) 7 |
|  |  | `0xF4` | 5 |

```c
struct posn add_posn(struct posn a, struct posn b);   // operators don't work on structs,
bool posn_eq(struct posn p, struct posn q);           // so write functions instead
```

## 3.17 The Five Sections of Memory

We model **five regions** (low → high address). Most of our work is on the **stack**; the **heap** comes later (dynamic memory).

| Region | Holds | Created |
|---|---|---|
| **Code** | compiled **machine code** | at compile time |
| **Read-Only Data** | global **constants** | before `main`, at compile time |
| **Global Data** | global **mutable variables** | before `main`, at compile time |
| **Heap** | dynamic memory (later) | at run time |
| **Stack** | each call's **parameters + locals + return address** | per function call |

> [!success] Which section is an identifier in? (exam goal)
> - **global constant** → Read-Only Data · **global mutable variable** → Global Data
> - **parameter or local variable — even a `const` local** → **Stack**
> - a **function's code** → Code
>
> Globals are found by scanning the whole program **before `main` runs**, then reserved and initialized (**at compile time**). Locals are reserved **only when their function is called**.

> [!note] Segments & temporary results
> Sections are grouped into hardware **segments**; accessing memory outside a segment is a **segmentation fault** (CS 350). Intermediate results of an expression (e.g. `f(3)` in `a = f(3) + g(4) - 5`) use **temporary storage** we don't model here (CS 241).

## 3.18 The Call Stack & Stack Frames

A **stack** (like a stack of plates) only lets you touch the **top**: you **push** on and **pop** off. The **call stack** records the **history of active calls** — if `main` calls `red` which calls `green`, the stack (top-down) is `green / red / main`; when `green` returns it is **popped** and control returns to `red`.

> [!success] Stack frame — one per call
> Each function call pushes a **stack frame** containing exactly:
> 1. the **argument values** (copies — see below),
> 2. **all local variables** declared anywhere in the function (mutable *and* `const`, including inner blocks),
> 3. the **return address** (where to resume in the **calling** function).

> [!danger] Pass by value — C copies arguments
> Before a call, every argument is evaluated to a **value**; C places a **copy** of each into the new frame. This is the **pass-by-value** convention — the callee works on **copies**, so mutating a parameter **never** affects the caller.

> [!danger] Return address = where it returns TO
> The return address is the location **in the calling function** to jump back to — e.g. `foo` calling `bat` on line 12 → `bat`'s return address is **`foo:12`**; `main`'s is the **OS** (the OS calls `main`). There is **exactly one** return address per frame, and it has **nothing to do** with the `return` statement(s) inside the called function (a `void` function has a return address too).

> [!note] Automatic (local) variables
> Local variables are **automatic**: space is reserved **when the function is called** (inside its frame) and **disappears** when it returns (the frame pops). `???` marks a local whose definition hasn't run yet. (`auto` is a real but rarely-used keyword.)

**The full model.** *Calling a function:* (1) **push** a new stack frame; (2) store the current location as the **return address**; (3) copy each **argument** into the frame; (4) set the program location to the **start** of the function; (5) initialize each local **when its definition is reached**. *Returning:* (a) set the location back to the **return address**; (b) **pop** the frame; (c) the return value goes to a temporary area (not modelled; CS 241).

**Worked call stack** — paused at line 2:

```c
int bat(int n) {           // 1
  return n * 2;            // 2   <-- paused here
}                          // 3
int foo(int x) {           // 10
  int b = x / 3;           // 11
  int res = 64 + bat(b);   // 12
  return res;              // 13
}                          // 14
int main(void) {           // 16
  int ans = foo(50);       // 17
}                          // 18
```

```
=========================   <- top (most recent call)
bat:
  n: 16
  return address: foo:12
=========================
foo:
  x: 50
  b: 16                     (50 / 3 = 16)
  res: ???                  (line 12 not finished)
  return address: main:17
=========================
main:
  ans: ???                  (foo hasn't returned)
  return address: OS
=========================
```

As `bat` returns 32, `foo`'s `res` = 64 + 32 = 96; `foo` returns 96 → `main`'s `ans` = 96; the frames pop in turn.

> [!example] Exam — stack-frame tracing
> You'll be asked to **draw the memory / call stack at a given line** (see the drawing rules in §3.21): a timeline of frames, `???` for undefined values, correct return addresses.

## 3.19 Recursion in Memory

Each **recursive call is just another stack frame** with its own copy of the parameters/locals; the only twist is that its **return address points back into the same function**. That's why `downup` (§2.15) prints `n` before *and* after its recursive call — each level keeps its **own `n`**.

```c
// sum_first(n) returns 0 + 1 + ... + n
int sum_first(int n) {          // line 3
  if (n == 0) return 0;         // line 4
  return n + sum_first(n - 1);  // line 5
}
```

Paused in the deepest call of `sum_first(2)` (first called from `main:10`):

```
=========================
sum_first:  n: 0   return address: sum_first:5
=========================
sum_first:  n: 1   return address: sum_first:5
=========================
sum_first:  n: 2   return address: main:10
=========================
main:       a: ???  return address: OS
=========================
```

## 3.20 Stack Growth & Stack Overflow

The call stack lives in the **Stack** section. The **bottom** (the `main` frame) is at the **highest** address; each new frame goes at a **lower** address — the stack **grows downward**:

```
low   Code │ Read-Only Data │ Global Data │ Heap  ↓ … ↑  Stack   high
```

> [!warning] Stack overflow
> If the stack grows too far it **collides** with other sections — a **stack overflow**, caused by **very deep or infinite recursion**. This is a key reason to prefer **iteration** for deep repetition: a loop creates **no new frames**, while every recursive call pushes one.

## 3.21 Uninitialized Memory & Drawing a Snapshot

> [!warning] Uninitialized: globals vs locals
> An uninitialized **global** is **automatically set to 0** (still initialize it explicitly, for style). An uninitialized **local** holds an **arbitrary** value — often **leftover bytes from a previous stack frame** — and edX warns about it (set it to `0` to silence the warning).

> [!success] Drawing a memory snapshot ("draw memory when line 19 is reached")
> - Show **Global Data** and **Read-Only Data** **separately** from the **Stack**.
> - Include **every** local in each frame — **even definitions not yet reached**; give those (and any not-yet-initialized or out-of-scope) the value **`???`**.
> - **Don't** show temporary/intermediate storage.
> - A variable defined **inside a loop** appears **once** in the frame and is **re-used** each iteration (re-initialized every pass — it does **not** keep the previous value).

> [!note] Scope ≠ memory
> **Scope** is a *syntax* rule (where a name is visible); **memory** is the *model* (what's stored). A variable sits in its frame **whether or not it is currently in scope**, until the frame is popped — so an **out-of-scope** variable still appears in the snapshot, shown as **`???`**.

## 3.22 The Model: Program State

> [!success] Program state = location + memory
> At any instant, a program's **state** is **(1) the current program location** plus **(2) the current contents of memory**. To trace a program by hand, track **both**.

## 3.23 Section Summary

- Model C with **control flow + memory** (not substitution); a program's **state** = location + memory.
- Use all control-flow statements (`return`, `if`, `while`, `do`, `for`, `break`, `continue`); **convert recursion ↔ iteration**.
- `int` is 4 bytes → **overflow** is undefined; `char` is 1 byte (**ASCII**); `sizeof` is an **operator**.
- Use **`struct`**s (dot operator; `=` copies; write your own equality; `sizeof` ≥ sum of fields, with padding).
- Name the five memory sections and **which one an identifier lives in**; globals at compile time, locals per call.
- A **stack frame** = argument **copies** (pass by value) + **all locals** + **one return address**; recursion = more frames; deep recursion → **stack overflow**.
- **Draw a memory snapshot** at a given line (`???` for not-yet-initialized / out-of-scope; one slot per loop variable).

---

# Topic 4 — Pointers

> [!abstract] Goal & objectives
> **Primary goal:** use **pointers** in C. By the end you should be able to:
> - **define and dereference** pointers; use the new operators **`&`**, **`*`**, **`->`**;
> - **describe aliasing**; use **`scanf`** to read input;
> - use **pointers to structures as parameters** (and explain why parameters are often struct pointers);
> - explain **when a pointer parameter should be `const`**; use **function pointers**.
>
> *Optional readings: CP:AMA 11, 17.7.* Pointers are **fundamental** to C and reappear in arrays (§5), dynamic memory (§8), and linked structures / lists & trees (§9). *(Notes so far cover 4.1 — Introduction; 4.2–4.4 to follow.)*

## 4.1 The Address Operator (`&`)

C was designed to give programmers **"low-level" access to memory** and to **expose the underlying memory model**.

> [!success] `&` — the address operator
> `&x` produces the **location** (the **starting address**) of an identifier in memory. The `printf` specifier **`%p`** displays an address (in **hex**).

```c
// Example 4.1.1 — the address operator
#include "cs136.h"
int g = 42;

int main(void) {
  printf("the value of g is %d\n", g);     // 42
  printf("the address of g is %p\n", &g);  // e.g. 0x55f... (hex)
}
```

## 4.2 Pointers & Pointer Types

> [!success] What a pointer is
> A **pointer is a variable that stores a memory address.** Define one by placing a **star `*`** before the identifier. A pointer's **type is the type of the data it points at** — for every type `T` there is a pointer type `T *`.

- `int *pi` — an **integer pointer** (`int *`): stores the address of an `int`.
- `char *pc` — a **character pointer** (`char *`).
- `struct posn *pp` — a **`struct posn` pointer**.

```c
// Example 4.1.2 — pointer types
#include "cs136.h"
struct posn { int x; int y; };

int main(void) {
  int i;                  // i is an int [uninitialized]
  i = 42;
  char c = 'z';
  struct posn p1 = {3, 4};

  int *pi;                // pi is a pointer to an int [uninitialized]
  pi = &i;                // pi now stores the address of i ("pi points at i")
  char *pc = &c;          // pc points at c
  struct posn *pp = &p1;  // pp points at p1

  printf("i = %d, address %p\n", i, pi);
  printf("c = %c, address %p\n", c, pc);
  printf("address of the struct posn is %p\n", pp);
}
```

## 4.3 Pointer Initialization & Style

> [!warning] The `*` belongs to the name, not the type
> In a definition the `*` is **part of the declarator**, not part of the variable name — the variable above is `q`, **not** `*q`. C ignores whitespace, so these are identical:
> ```
> int *p = &i;   // style A  ← used in this course & CP:AMA
> int * p = &i;  // style B
> int* p = &i;   // style C
> ```
> Style A makes **multiple definitions** unambiguous:
> ```
> int *p1 = &i, *p2 = &j;   // VALID: both are pointers
> int * p1 = &i, p2 = &j;   // INVALID: p2 is NOT a pointer, just an int
> ```

```c
// Example 4.1.3 — pointer initialization
#include "cs136.h"
int main(void) {
  int i = 42;
  int *p = &i;    // p defined AND initialized

  int *q;         // q defined [uninitialized]
  q = &i;         // q now points at i

  //*q = &q;      // "q points at q"? — type error (see §4.4)

  int *p1 = &i;   // p1 points at i
  int **p2 = &p1; // p2 points at p1  (pointer to a pointer)
}
```

## 4.4 Pointers to Pointers

- **Can a pointer point at itself?** No. `*q = &q;` is a **type error**: `q` has type `int *` (it can only point at an `int`), but `q` itself is **not** an `int`.
- You *can* have a **pointer to a pointer**: `int **p2 = &p1;` — the type of `p2` is **`int **`** ("pointer to a pointer to an int"). C allows any number of levels, but **more than two is uncommon**.
- A **`void *`** (void pointer) can point at **anything**, including itself — covered in the last module.

## 4.5 Pointer Values & `NULL`

A pointer is an ordinary **variable storing a value** — it's only "special" because that value is an **address**. So a pointer **also has its own address**.

> [!note] Pointer diagram (recreated from the course)
> `int i = 42; int *p = &i;` — the **value of `p`** equals the **address of `i`**, but the **address of `p`** is different. We usually don't care about the numeric address and just draw an **arrow**:
> ```
>        +------+
>   p ──▶ |  42  |  i     (p "points at" i; *p is 42)
>        +------+
>   p has its own, different address (&p)
> ```

> [!success] `NULL` — "points at nothing"
> `NULL` is a special pointer value meaning the pointer points at **nothing**. If a pointer's target is unknown at definition, or becomes invalid, **assign it `NULL`** (a "NULL pointer"). `int *p1 = NULL;` is **much better style** than an uninitialized `int *p;` (which warns in edX).

> [!tip] `NULL` is false
> In C `false` is `0`, and **`NULL` is also "false"** in a Boolean context. So `if (p)` is equivalent to `if (p != NULL)` — both are good style. A pointer's value can be **mutated** with `=` like any variable (`p2 = &j;` then `p2 = NULL;` then `p2 = &i;`).

```c
// Example 4.1.4 — pointer values and NULL (abridged)
int i = 42;
int *p = &i;
trace_ptr(&i);          // address of i
trace_ptr(p);           // same address (p's value)
trace_ptr(&p);          // DIFFERENT — p's own address

int *p1 = NULL;         // GOOD style
if (p1 != NULL) { /* ... */ } else { printf("p1 is NULL!\n"); }
```

## 4.6 `sizeof` a Pointer

> [!note] All pointers are the same size
> On a **k-bit** system, addresses are **k bits**, so a pointer needs **k bits**. In the 64-bit edX environment **`sizeof` any pointer is 8 bytes (64 bits)** — **regardless** of the type it points at (`sizeof(int *) == sizeof(char *)`).

## 4.7 The Dereference Operator (`*`)

> [!success] `*` — dereference (indirection), the inverse of `&`
> **`*p` produces the value of what `p` points at.** Since `&` "gets the address of this box", `*` "**follows the arrow to the next box and gets its contents**". So `*&i` (and even `*&*&*&i`) is just the value of `i`.

- **Dereferencing `NULL` crashes** your program.
- The `*` symbol has **three distinct uses** in C: **multiplication** (`i * i`), **pointer definitions/types** (`int *p`), and **dereference** (`*p`).

```c
// Example 4.1.5 — sizeof and the dereference operator
#include "cs136.h"
int main(void) {
  trace_int(sizeof(int *));   // 8 (bytes)
  trace_int(sizeof(char *));  // 8  — * in a pointer TYPE

  int i = 42;
  int *p = &i;                // * in a pointer DEFINITION
  trace_int(*p);              // 42 — * as DEREFERENCE

  p = NULL;
  // trace_int(*p);           // dereferencing NULL → crash

  int k = i * i;              // * as MULTIPLICATION
  trace_int(k);              // 1764
}
```

## 4.8 Dereferencing Pointers to Structures (`->`)

The field-selection operator `.` has **higher precedence** than `*`, so reaching a field through a struct pointer needs awkward parentheses: **`(*ptr).field`**. The **arrow operator `->`** combines dereference + selection:

> [!success] `ptr->field` ≡ `(*ptr).field`
> Use the **arrow `->`** — it's less awkward than `(*ptr).field`.

```c
// Example 4.1.6 — the dereference-selection ("arrow") operator
#include "cs136.h"
struct posn { int x; int y; };
int main(void) {
  struct posn my_posn = {0, 0};
  struct posn *ptr = &my_posn;

  (*ptr).x = 3;   // awkward
  ptr->y = 4;     // much better
  trace_int(ptr->x);  // 3
  trace_int(ptr->y);  // 4
}
```

## 4.9 Aliasing

> [!warning] Aliasing
> **Aliasing** occurs when the **same memory address can be reached through more than one pointer**. The key is distinguishing changing the **pointer** (`p = q;`) from changing **what it points at** (`*p = *q;`).

**`p = q;` — pointer assignment** ("change `p` to point at what `q` points at"): copies `q`'s *value* (an address) into `p`. The pointed-at data is untouched.

> ```
> int i = 5, j = 6;  int *p = &i;  int *q = &j;
>
> before  p = q;                 after  p = q;
>   i: 5  ◀── p                    i: 5            (i unchanged)
>   j: 6  ◀── q                    j: 6  ◀── p, q  (p now points at j)
> ```

**`*p = *q;`** — changes the **value of what `p` points at** (not `p` itself):

> ```
> p = &i;  *p = *q;              (p points at i, q points at j)
>
> before  *p = *q;               after  *p = *q;
>   i: 5  ◀── p                    i: 6  ◀── p   (i becomes j's value, 6)
>   j: 6  ◀── q                    j: 6  ◀── q   (p unchanged — still points at i)
> ```

```c
// Example 4.1.7 — aliasing (abridged)
int i = 5, j = 6;
int *p = &i;
int *q = &j;

p = q;      // p's address changes; i does NOT   → p,q point at j; i == 5
p = &i;
*p = *q;    // i changes to 6; p does NOT         → i == 6

int *p1 = &i;
int *p2 = p1;     // p1 and p2 alias i
int **p3 = &p1;
*p1  = 10;        // i == 10
*p2  = 100;       // i == 100  (via a different alias)
**p3 = 1000;      // i == 1000 (same as *(*p3))
```

---

# Glossary

*Key terms per chapter. Jump to a term's chapter for the full treatment.*

## Topic 1 — Functional C

- **C / history** — created by **Dennis Ritchie (1969–73)** for Unix portability; successor to **B** (from **BCPL**); low-level, machine-code-friendly.
- **C99** — the C standard used in this course (vs C11/C18/C23).
- **Compiled vs interpreted** — C is **compiled** (translated to machine code; must be error-free to produce an executable); Racket is **interpreted**.
- **File types** — `.c` implementation, `.h` header, `.o`/`.ll` compiled object, **`a.out`** executable.
- **`main`** — the single **entry point**; `int` return type; returns the **exit/error code** (`errno`), **0 = success** (**`EXIT_SUCCESS`**); non-zero fails Marmoset.
- **No top-level code** — statements/expressions run **only inside functions**.
- **`#include` / header** — copies library declarations in; `stdio.h` (for `printf`/`scanf`), `cs136.h` (course bundle).
- **`printf`** — library output function; a **side effect**.
- **Tracing** — `trace_int`, `trace_msg`, `trace_bool`, `trace_char`, `trace_ptr`; write to **stderr**; ignored by tests.
- **Comment** — `//` to end of line, `/* … */` multi-line; **no nesting**.
- **Infix / associativity** — C uses infix with parentheses; operators are **left/right associative** (most arithmetic is left-associative).
- **No exponent operator** — `^` is **bitwise XOR**, not power.
- **`/` and `%`** — integer `/` **truncates toward zero** (C99); `%` is remainder (`i%j = i-(i/j)*j`), sign of `i`; avoid with negatives.
- **Octal/hex literals** — a **leading `0` = octal** (`017`=15), `0x` = hex (`0x10`=16); never add a leading zero by accident.
- **Static typing** — types fixed before running and can't change → **no "type" runtime errors**, no `integer?`/`string?`; bad types **won't compile**. Omitting a type defaults to `int` (with a warning).
- **Identifier** — starts with a letter; letters/digits/underscores; **`snake_case` required**; avoid leading `_`.
- **Argument vs parameter** — arguments are **passed** in a **call**; parameters are the function's named inputs.
- **Terminology** — C: **call / pass / return**; Racket: **apply / consume / produce**.
- **`void` parameters** — `f(void)` = no parameters; call with `f()`; always write `void`.
- **No nested functions** — functions can't be defined inside functions.
- **Declaration vs definition** — declaration gives only the type (a **prototype**, header + `;`); definition creates it (and includes a declaration). **Declared many times, defined once**; must be declared **before use** (else "implicit declaration").
- **Documentation** — purpose (example call + *what*, not *how*) + `// requires:`; no contract-type comment.
- **CS 136 style** — brace at end of line; closing brace aligned on its own line; 2–4-space indent; spaces after commas/around operators.
- **Boolean values** — produce **1/0**; `true`/`false` predefined; **only 0 is false**, any non-zero is true (`NULL` also false).
- **Comparison / logical operators** — `== != < <= > >=`; `! && ||` with **short-circuiting**; single `&`/`|` is a bug.
- **Order of operations** — `!` > `* / %` > `+ -` > `< <= >= >` > `== !=` > `&&` > `||`.
- **`bool`** — an integer restricted to 0/1.
- **`assert(exp)`** — halts with a message if `exp` is false; used to **test** and to **verify requirements**; infeasible requirements marked `[not asserted]`; use several small asserts.
- **Design recipe** — purpose + requirements (asserted); the contract is in the types.
- **Control flow** — blocks, function calls, conditionals, iteration; `return` ends the function (code after it never runs).
- **`if`/`else`/`else if`** — only controls flow; C's `if` **does not produce a value** (unlike Racket `cond`). Use braces (avoid **dangling else**).
- **Ternary `?:`** — `q ? a : b` **does** produce a value; use sparingly.
- **`switch` / `goto`** — **banned** (fall-through / spaghetti code).
- **Simple recursion** — base + recursive case; leaves work for the return (more memory).
- **Accumulative recursion** — wrapper + helper with an **accumulator**.
- **Mutual recursion** — two functions call each other (e.g. `is_even`/`is_odd`); needs a **forward declaration**.

## Topic 2 — Imperative C

- **Functional vs imperative** — pure (return-only, constants) vs impure (variables, statements, **side effects**).
- **Side effect** — a change of **state** (a value at a moment in time). Kinds: **output**, **input**, **mutation**.
- **`effects:` documentation** — e.g. `// effects: produces output` / `reads input`; use **"may"** for conditional effects; don't document `assert`/tracing.
- **I/O terminology** — functions are **passed** args and **return** values; only I/O functions **read input** / **produce output** — don't conflate output with the return value.
- **`printf`** — output; format specifier **`%d`** (decimal int); escapes `%%`, `\\`, `\"`; **returns an `int`** = number of characters printed (**not `void`**).
- **`void` function** — returns nothing; `return;` optional; a **procedure** mathematically.
- **Expression statement** — expression + `;`; value **discarded**; exists for side effects.
- **Three statement types** — compound (blocks), expression statements, control-flow. (A variable **definition** is not a statement.)
- **Variable definition** — type + name + (initial value) + `=` + `;`; **always initialize**.
- **Initialization ≠ assignment** — same `=`, different semantics.
- **Mutation** — changing a variable's value (a side effect).
- **Assignment operator `=`** — stores RHS into LHS variable; **not symmetric**; also **produces** the RHS value (`x=y=z=0`); keep **one side effect per statement**.
- **`=` vs `==`** — single `=` assigns (always "true"); use `(13 == i)` defensively.
- **Compound assignment** — `+= -= *= /= %=`.
- **Increment/decrement** — `++`/`--`; **postfix `x++`** yields old value then increments; **prefix `++x`** increments then yields new; prefer prefix.
- **`const`** — immutable "variable"; communicates intent, prevents accidental mutation, may optimize.
- **Scope** — where an identifier is visible; **global** = below its definition; **local** = **block scope**.
- **Shadowing** — inner name hides an outer one (poor style); C99 allows locals anywhere; declare in narrowest scope.
- **Pure vs impure** — impure = has a side effect **or** depends on a global mutable variable.
- **Mutating locals/parameters** — not a side effect (stays pure); **mutating a global** is a side effect.
- **Global mutable variables** — **banned** (Rule 12.1); **global constants** encouraged; **static local variables** (persist between calls) also banned.
- **`scanf`** — reads input; `scanf("%d", &x)` mutates `x` (the **`&`** is its address); **read one value per call**; returns the **count read** or **`EOF`** (invalid → 0; 1 = success for a single read); **skips whitespace**; one call = read + mutate `x` + mutate `retval`.
- **`[Run Code]` vs `[Run With Tests]`** — Stdin box vs `.in`/`.expect` files.
- **stdout vs stderr** — `printf` → stdout (tested); tracing → stderr (not tested).
- **Testing harness** — reads args from input, calls the function, prints results (symbols via `lookup_symbol`/`read_symbol`).
- **Testing terminology** — assertion, I/O-driven, harness, **white-box** vs **black-box**, **unit**, **regression**.

## Topic 3 — The C Model

- **C's model** — **control flow + memory** (vs Racket's **substitution**). A program's **state** = current **program location** + **memory contents**.
- **Program location / program counter** — "where" execution is; starts at the top of `main`.
- **`if`-rule** — evaluate the condition, run one branch.
- **`while` loop** — `while (expr) stmt`; condition checked **before** the body, so it may run **zero** times; relies on mutation (else endless loop).
- **Iteration vs recursion** — loops vs function calls; **convertible both ways**; accumulative recursion ↔ `while` + accumulator variable. Iteration avoids growing the stack.
- **`do`/`while`** — body runs **at least once** (condition checked at end); handy for input validation.
- **`break`** — terminates the **innermost loop** only (you **can't `break` out of an `if`**); vs **`return`** (exits the whole function).
- **`continue`** — skips the rest of the current block, continues the loop; always removable with `if`/`else`.
- **`for` loop** — `for (setup; expr; update)`; shorthand for a `while`; **C99** setup may be a definition (block scope); any part omittable (omitted expr = true); comma operator exists (don't use). **for-vs-while + `continue` trap:** `for` still runs the update, a hand-written `while` skips it → infinite loop.
- **Bit / byte / address** — bit = 0/1; **byte = 8 bits** (256 states), the smallest addressable unit; its position = **address** (hex). 1 MB → `0x0 … 0xFFFFF`.
- **Variable = a location** — a name for an **address**; using it **fetches** the stored value (vs Racket's "name for a value"). Defining it: reserve space, track the address, store the initial value.
- **`sizeof`** — an **operator** (not a function) giving a type's/identifier's byte size; `sizeof(int)` = **4** (32 bits); real size = machine word size; `inttypes.h` has `int32_t`/`int64_t`.
- **`int` range** — `2³²` values, **−2³¹ … 2³¹−1** (`INT_MIN`/`INT_MAX`); `unsigned int` = `0 … 2³²−1` (unused); `sizeof` returns a `size_t` (unsigned).
- **Overflow** — exceeding the `int` range → **undefined behaviour** (may roll over, look random, or error). Racket avoids it with arbitrary-size numbers; C can via a big-int library (gmplib).
- **`char`** — a 1-byte integer (−128…127); for **characters**. **ASCII**: `'A'`=65, `'a'`=97, `'z'`=122, space=32, control 0–31 (bell=7), newline=10. Single quotes = a char (`'a'` is 97); **`"a"` ≠ `'a'`** (string vs char); print with `%c`/`%d`; character arithmetic avoids magic numbers.
- **Reading chars** — `scanf("%c", &c)` (every keystroke is a char); `"%d"` skips whitespace but `"%c"` doesn't — use **`" %c"`** (leading space) to skip it.
- **Symbols / `float` / `double`** — no Racket `symbol` (use `ALL_CAPS` int constants / `enum`); `float`/`double` are inexact (`%f`), have precision error, **not used** unless told.
- **`struct`** — composite type (`struct` + braces + trailing `;`, field types required); **dot** operator selects a field; designated init `{.y=4,.x=3}` (omitted fields = 0); `=` **copies all fields** but `{…}` is init-only (`p={5,6}` invalid); `==`/`printf` don't work on structs → write functions / print fields. Definition reserves **no** memory; `sizeof` ≥ sum of fields (**padding/alignment**, order matters).
- **Five memory sections** — **Code** (machine code), **Read-Only Data** (global constants), **Global Data** (global mutables), **Heap** (later), **Stack** (params + locals + return address). Globals created before `main` at compile time; locals per call. **Which section:** global const → RO data; global mutable → global data; **param/local (even `const`) → stack**.
- **Segmentation fault** — accessing memory outside a hardware **segment**.
- **Call stack** — LIFO (push/pop, top only); records the **history of active calls**.
- **Stack frame** — one per call: **argument copies** + **all local variables** (incl. inner blocks, `const`) + **one return address**.
- **Pass by value** — C copies each argument into the frame; mutating a parameter never affects the caller.
- **Return address** — the location **in the calling function** to resume at (`foo:12`); `main` → **OS**; exactly one per frame; unrelated to `return` statements.
- **Automatic variables** — locals; space reserved on call, freed (popped) on return.
- **Calling/returning model** — call: push frame, save return address, copy args, jump to start, init locals as reached. Return: restore location, pop frame.
- **Recursion in memory** — each call = a new frame; its return address points back into the **same** function; deep/infinite recursion → **stack overflow** (stack grows toward lower addresses and collides).
- **Uninitialized memory** — global → auto **0**; local → **garbage** (leftover from a previous frame).
- **Memory snapshot** — show global/RO separate from stack; include all locals (not-yet-reached / out-of-scope = **`???`**); skip temporaries; a loop variable has **one** slot, re-initialized each pass.
- **Scope ≠ memory** — scope = syntax (visibility); memory = model; a variable stays in its frame (shown `???` when out of scope) until the frame pops.

## Topic 4 — Pointers

- **Address operator `&`** — `&x` gives the **address** (location) of `x`; print an address with `%p` (hex).
- **Pointer** — a **variable that stores a memory address**; defined with a **`*`** before the name.
- **Pointer type** — `T *` points at data of type `T` (`int *`, `char *`, `struct posn *`); the `*` belongs to the **name**, not the type (`int *p1, *p2;`).
- **Style** — `int *p` (style A, used here) = `int * p` = `int* p`; in multi-definitions each pointer needs its own `*`.
- **Pointer to pointer** — `int **p` ("pointer to a pointer to an int"); a pointer **can't point at itself** (type error); `void *` points at anything (later).
- **`NULL`** — pointer value meaning "**points at nothing**"; initialize unknown pointers to `NULL` (not uninitialized); `NULL` is **false**, so `if (p)` ≡ `if (p != NULL)`.
- **`sizeof` a pointer** — same size for **all** pointer types (8 bytes / 64 bits in the edX environment), regardless of the pointed-at type.
- **Dereference `*`** — `*p` = the **value** `p` points at (inverse of `&`); `*&i` = `i`; dereferencing `NULL` **crashes**. `*` has **3 uses**: multiply, pointer def/type, dereference.
- **Arrow `->`** — `ptr->field` ≡ `(*ptr).field` (needed because `.` binds tighter than `*`); prefer `->`.
- **Aliasing** — the same address reachable via **>1 pointer**. `p = q;` changes **which box `p` points at** (data untouched); `*p = *q;` changes **the value in the box `p` points at** (`p` untouched).
