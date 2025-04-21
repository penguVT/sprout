# Sprout

**NOTE: This language spec was created with chatgippity and is experimental. I haven't properly vetted the grammar or thought through the potential pitfalls of the spec yet. If you notice any glaring issues, please make a PR with the corresponding changes / fixes.** 

Sprout is a statement-oriented language with:

- **Scalar types**: `int`, `bool`
- **Variables** with lexical scope
- **Expressions** with the usual arithmetic / logical operators
- **Control flow**: `if ... else`, `while`
- **Procedures** (no return values) to enable multi-file growth

There is **no implicit type conversion**, no operator overloading, and no standard library beyond built-ins (`print`, `input`).

The program's entry point is a procedure named `main`.

## Lexical Structure

| Category | Definition |
| -------- | ---------- |
| Whitespace | Space, tab, carriage return, newline. Ignored except to separate tokens. |
| Comments | `// … <newline>` single‑line. No block comments. |
| Identifiers | `letter ( letter` |
| Keywords | `int` `bool` `if` `else` `wproc` `return` `print` `input` `true` `false` |
| Integer literal | `digit+` (32‑signed). |
| Boolean literal | `true` `false` |
| Operators | `+  -  *  /  % ==  !=  <  <=  >  >=  &&` |
| Delimiters | `;  ,  ( )  {` |

## Grammar

See [grammar.ebnf](grammar.ebnf) for the full spec.

Operator precedence: highest at unary, lowest at assignment; all binary operators are left-associative expect assignment (right associative).

## Static Semantics (Type Rules)

1. **Variables** must be declared before first use within the current scope.
2. **Assignments:** The left-hand identifier must have the same declared type as the right-hand expression
3. **Operators:**
    - `+ - * / %` accept `int, int -> int`.
    - `== != < <= > >=` accept `int x int` or `bool x bool` and yield `bool`
    - `&& || !` accept / yield `bool`
4. **Condition expressions** in `if` and `while` must be `bool`.
5. **Procedure calls:** argument count and types must match the formal parameter list.
6. `return` inside a procedure ends execution of that procedure (procedures have no value).

A compiler can enforce these with a single symbol table stack.

## Runtime Semantics

- **Memory model:** flat;  each procedure call pushes a new frame containing its locals and parameters.
- **Integers:** 32-bit two's complement; overflow is implementation-defined.
- **Booleans:** 1-byte; `true` == 1, `false` == 0.
- **Call convention:** pass-by-value, caller-allocates arguments on stack, callee pops.
- **Built-ins:**
    - `print(x)` - write decimal `int` or `bool` to stdout followed by newline.
    - `input()` - read an `int` from stdin (blocking).

## Example Programs

See the `examples/` directory.

## Potential Extensions

Some potential extensions for the language include:

- **Return values:** extend `proc` with `-> type` and allow `return expr;`
- **Arrays / pointers:** grammar can add `[` and `]`. The logic for C-style pointers would be more complex, and may require greater effort.
- **User types:** `struct`, `enum`
- **Modules:** `import` keyword with file-based units.
