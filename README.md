# Flowgorithm C Language Template

This repository contains an unofficial **Flowgorithm language template for C**, created to allow students to translate flowcharts into fully compilable C programs.  
The project is intended for **educational use**, with an emphasis on clarity, predictability, and adherence to standard C (C99+).

The template provides:
- A complete `.fpgt` file that integrates C into Flowgorithm's code-generation system.
- Several helper functions required to support strings, booleans, and typed input.
- Example Flowgorithm programs (in the `Examples/` folder) together with their generated C code.

The goal is to offer a reliable tool that mirrors as closely as possible the behaviour of Flowgorithm’s internal interpreter, while still producing valid and idiomatic C code suitable for beginners learning the language.

---

## Purpose of This Template

Flowgorithm officially supports languages such as C#, Java, Python, and C++.  
However, it lacks a native C generator.  
This template fills that gap by implementing:

- Type mappings from Flowgorithm → C  
- Expression translation  
- Function generation (with and without return values)  
- Array declaration  
- String and boolean emulation  
- Typed input and formatted output  
- Helper functions for string conversion, concatenation, numeric parsing, and boolean parsing

The generated code targets a **minimal and didactically clean subset of C**, avoiding pointers, dynamic structures, or advanced features unless they are strictly required to emulate Flowgorithm semantics.

---

## Challenges in Translating Flowgorithm to C

C is a low-level language that lacks many of the high-level features available in Flowgorithm or languages like C# and Java.  
To maintain compatibility with Flowgorithm's execution model, several non-trivial problems had to be solved.

Below is an overview of the main design challenges and the chosen solutions.

---

### 1. Typed Input and Conversion Functions

Flowgorithm accepts user input interactively and converts it to the proper type.  
C does not provide such behaviour natively, therefore this template defines custom helpers:

- `inputValue()` — reads a full line and parses it as `double`
- `inputInt()` — parses an integer
- `inputBool()` — parses a boolean using Flowgorithm’s rules
- `inputString()` — reads an entire line, preserving spaces

Parsing is done using `fgets()` combined with `strtod`, `strtol`, or custom string analysis.  
This ensures that invalid input does not break the program: instead, the functions loop until a valid value is provided, reproducing Flowgorithm’s behaviour as closely as possible.

---

### 2. Boolean Support

In standard C, booleans are extremely primitive (`bool` from `<stdbool.h>` is just an integer).  
Flowgorithm, instead, uses the strings `"true"` and `"false"` during input and output.

To match this behaviour:

- Boolean input accepts only `"true"` or `"false"` (case-insensitive, no spaces).
- Boolean output prints exactly `"true"` or `"false"`.
- Internally, C still stores booleans as `bool`.

This approach preserves the Flowgorithm model without confusing beginners about how booleans work in real-world C code.

---

### 3. String Handling

Strings are the most delicate part of the translation.  
Flowgorithm treats strings as high-level immutable values.  
C, instead, has only null-terminated byte arrays, without automatic resizing or concatenation.

This template implements:

- All strings as `char*` obtained via `malloc`
- A safe `stringConcat(const char*, const char*)` that returns newly allocated strings
- `inputString()` that dynamically allocates and returns the full user input
- `toString(double)` using a static buffer for temporary conversions, as expected in C

This makes string expressions like:

result = "literal one" + s1 + "literal two" + s2


work correctly, even when chained.

Memory is intentionally not freed automatically so as not to overwhelm beginners with memory management: Flowgorithm itself assumes automatic lifetime.

---

### 4. Array Size: Limitations of C

Flowgorithm supports an intrinsic function `Size(array)`.

In C, however:

- Array length can be computed using `sizeof(array) / sizeof(array[0])`
- **But only inside the same scope where the array is declared**
- When an array is passed to a function, it decays to a pointer (`int*`, `double*`, etc.), losing all size information.

For this reason, the first release adopts a simple didactic rule:

> Arrays are declared using standard C syntax, but `Size(array)` is only valid in the scope of the declaration.  
> When calling user-defined functions, the programmer must explicitly pass the number of elements.

Example:

```c
void bubbleSortInt(int a[], int n);

int arr[20];
int n = Size(arr);         // Works here
bubbleSortInt(arr, n);     // n must be forwarded
```

This limitation reflects the true behaviour of the C language and helps students learn correct array handling.

## Repository structure

flowgorithm-c-template/
│
├── C.fpgt                # The actual Flowgorithm language template for C
├── Examples/             # Flowgorithm sample files + generated C output
├── README.md             # Documentation (this file)
└── LICENSE               # Open-source license


## Contributing

Contributions are welcome.
Several areas can be extended or improved:

- Memory-safe string utilities
- Richer output formatting
- Optional free() calls for advanced users
- Support for file I/O
- More robust boolean parsing
- Extended math library integration
- Improvements to nested function generation

Feel free to open issues or pull requests.

## License
This project is released under the MIT License.
You are free to fork, extend, and redistribute it.


