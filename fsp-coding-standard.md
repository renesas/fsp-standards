# Overview

This document provides standards and coding styles to be adopted when developing Embedded C code. The objective of using these coding standards is to provide both our customers and fellow developers with a consistent high-quality experience when using Renesas supplied code.

## Terminology
The following terminology is used throughout this document:
| Term | Definition |
| ---- | ---------- |
| shall | Required rule; must be followed. |
| should | Not required, but highly recommended. |
| may | Optional; expressly allowed. |
| should not | Not recommended. |
| shall not | Not allowed under any circumstances. |

# 1. General Rules
1. All C code, whether production, template, or test code shall adhere to this Coding Standard.
    - In test code, developers are only responsible for enforcing the standard on changes made by them.
1. Rules in this document should be enforced through automatic code formatting, static analysis tools, and/or other automatic scripts and services wherever possible.
1. There shall be no references to confidential information in any public-facing files or documentation.
1. The character set for code and comments shall be limited to standard ASCII. Extended ASCII shall not be used.
1. Files shall be encoded UTF-8 using line breaks consistent with other files.
1. Third party code shall not be changed to meet the standards detailed in this document.
    1. Any other rule(s) may be ignored when required to directly interface with third-party code.

# 2. Toolchain

## 2.1. C Standard
1. All programs shall be written to comply with the ISO/IEC 9899:1999 (i.e. C99) C programming language standard.
1. The types provided in `stdint.h` and `stdbool.h` shall be used for all integer and boolean declarations except when directly interfacing with third-party code that provides its own.

## 2.2. Compiler Support
1. The use of proprietary compiler language keyword extensions, #pragmas, and inline assembly should be kept to the minimum necessary to get the job done.

## 2.3. Build Warnings and Errors
1. All code shall compile with no errors.
1. Code written by Renesas shall compile with no warnings.

# 3. Files

## 3.1. Naming
1. Source and header file names shall be composed as follows:
    1. A prefix:
        - `r_` for hardware drivers
        - `rm_` for software modules (middleware)
        - `bsp_` for BSP files
        - `fsp_` for common code that does not fall under one of the above categories
    1. The name of the module or BSP section (e.g. `adc`, `sci`, `motor_driver`, `clocks`)
    1. An optional description prepended with an underscore to differentiate between modes, protocols, etc. (e.g. `_i2c`, `_uart`)
        - For hardware drivers this should correspond directly to the mode or protocol name as given in the device manual.
    1. The file extension
1. Source and header filenames shall be all lowercase.
1. Where a header file provides an interface to a single C file, the header shall have the same name as the source file (excluding extension).
1. Any file containing a `main()` function shall have the word "main" in the filename.
1. Exceptions to the rules in this section are permitted for internal BSP files with an established naming convention.

## 3.2. Header Files (\*.h)
1. Header files shall contain a preprocessor guard against multiple inclusion.
    ```c
    #ifndef MODULE_H
    #define MODULE_H
    ...
    #endif /* MODULE_H */
    ```
1. If C++ support is required, the contents of header files shall be enclosed within `extern "C" { ... }`.
    - This should be done with a macro to allow for adjustment or other additions in the future.
1. Variable storage shall not be declared in a header.
1. `extern` references should not be used in header files.
1. Only items which other modules need to know about should be provided in the header; items not needed by other files should be placed in the source file.

## 3.3. Source Files (\*.c)
1. Source files shall include only the code needed to control one "entity", e.g.:
    - A peripheral driver
    - An encapsulated data type or active object
    - A port, communication, or shim layer between other entities
1. Absolute include paths shall not be used.
1. Relative include paths should be avoided unless the target header is in a folder that should not be added to the project include paths.
    1. Relative include paths shall use forward slashes.
1. Source files shall not include other source files.
1. System headers shall be included with angle brackets (e.g. `#include <math.h>`).
1. Project headers shall be included with double quotes (e.g. `#include "r_adc.h"`).
1. Source files should `#include` the header file of the same name (if applicable).
    - This allows the compiler to confirm that each public function and its prototype match.

# 4. Style

## 4.1. Line Length
1. All lines of C code shall be limited to a maximum of 120 characters.
1. Long or confusing lines should be split where needed to improve readability.
1. A line of code shall not contain more than one statement.

## 4.2. Braces `{ ... }`
1. Braces shall always surround the block of code following `if`, `else`, `switch`, `case`, `default`, `while`, `do` and `for` statements, even if the block is empty or contains only one line.
1. If a set of braces is empty, a comment should be added to explain.
    - e.g. `/* Wait for event */`, `/* No messages to process */`
1. Braces shall appear on their own line except:
    - when closing a type definition,
    - when closing a do-while loop, or
    - when initializing arrays where the array values fit on the same line as the symbol.

## 4.3. Parentheses `( ... )`

1. Parentheses shall be used in statements to explicitly specify the order of operations whenever the order could affect operation.
1. Parentheses should be used as needed to improve clarity and aid understanding.
1. Logical operator (`&&`, `||`) operands shall be surrounded with parentheses unless they are a single identifier or constant.
1. Split operations across multiple statements when operations at the same precedence level shall be executed in a particular order.

## 4.4. Comments `//`, `/* ... */`
1. Single-line (`//`) comments shall only be used on lines that also contain code. Otherwise, use standard block comments (`/* ... */`).
1. Comments shall not use Doxygen-style comment blocks (`/** ... */` or `///<`) unless the comment body is intended to be added to generated documentation.
1. If the first word of a comment is not type-sensitive, it shall be capitalized.
1. Comments shall not be nested.
1. "Todo"-type comments should not be present in a public release or release candidate.

## 4.5. Whitespace

### 4.5.1. Indentation
1. The tab character shall not be used.
1. Each indentation "level" shall be one space for preprocessor directives and four spaces for all other purposes.
1. Preprocessor directives shall be indented once for each level of nesting.
1. Everything inside a set of braces (between `{` and `}`) shall be indented one level beyond the braces.
1. Comment lines and blocks shall start at the same indentation level as the related code.
1. The following items should be aligned with the same fields on neighboring lines (if applicable):
    - Variable names
    - Struct entries
    - Macro definitions
    - Function arguments
    - Assignment operators (`=`, `+=` etc.)
    - Inline comments
1. A blank line should be added between blocks of definitions if different alignments would improve readability.
1. When a line is too long to fit the maximum line width, the additional lines should be indented in the most readable manner possible.

### 4.5.2. Spaces
1. Spaces shall be added around operators as follows:

    | Type | Leading | Trailing |
    | ---- | ------- | -------- |
    | Keywords (`if`, `for`, etc.) | No | Yes<sup>1</sup> |
    | Assignment (`=`, `+=`, etc.) | Yes | Yes |
    | Binary math/logic (`+`, `==`, etc.) | Yes | Yes |
    | Ternary (`?` and `:`) | Yes | Yes |
    | Comma (`,`) | No | Yes |
    | Pointer (`*`) | Yes<sup>2</sup> | Yes<sup>2</sup> |

    <sup>1</sup> No trailing space after `return` with no arguments or `else` when by itself
    <sup>2</sup> Only in declarations

1. A single space shall also follow:
    - Function names in full declarations
    - Semicolons within a `for` statement
1. Comments shall have a single space between the text and delimiter(s).
1. Except for the other rules in this section, all operators and symbols shall be joined with their closest operands.
    - This includes parentheses and brackets.

### 4.5.3. Blank Lines
1. Blank lines should be added as needed to separate code into natural, logical blocks within a function.
1. Every comment block should have a blank line before it.
1. Every file shall end with a blank line.

## 4.6. Organization
1. Top-level elements of a file shall be grouped into sections by type under a comment heading in the below style:
    ```c
    /**********************************************************************************************************************
    * {Section name}
    **********************************************************************************************************************/
    ```

1. The following standard sections should be used where applicable in the order specified below:
    1. **Includes**
    2. **Macro definitions**
    3. **Type definitions**
        - Enumerations should be declared before structs.
    - Header file specific sections:
      1. **Exported global variables**
      2. **Public APIs**
          - Should contain public function prototypes only
          - Should be ordered to match the API it implements, if applicable
    - Source file specific sections:
      1. **Private function prototypes**
      2. **ISR prototypes**
      3. **Private global variables**
      4. **Global variables**
      5. **Public functions**
          - Functions in this section should be ordered to match the API it implements, if applicable
      6. **Private functions**
          - Functions in this section should be organized logically in such a way that related code is grouped together.
      7. **Interrupt service routines**
1. Section headings shall be removed if there are no related contents.

## 4.7. Naming

### 4.7.1. General
1. Names should be descriptive but not verbose, using whole words and common abbreviations wherever possible.
1. No element shall have a name that is a keyword, variable, or other predefined symbol of C, C++, their standard libraries, or any other well-known extension of the C programming language, including specifically K&R C and C99.
    - Examples include `interrupt`, `inline`, `class`, `true`, `false`, `public`, `errno`, etc.
1. Excepting linker sections, named elements shall not begin or end with an underscore (`_`).

### 4.7.2. Macros
1. Macro names shall be all-caps and include only alphanumeric characters and underscores (`_`).
1. Macro definitions shall be wrapped in parentheses.
1. Where units (e.g. MHz, ms) are important, they shall be clearly specified in the name.

### 4.7.3. Type Definitions
1. All top-level structures, unions, and enumerations shall be named via `typedef`.
1. Type names shall include only lowercase alphanumeric characters and underscores with a suffix of `_t` (e.g. `i2c_mode_t`).
1. Type names should start with the filename root (i.e. `adc_source_t` for `r_adc.h`).
1. All type definitions shall have a tag comprised of a type-based prefix followed by the base name of the type (i.e. type name without `_t`):
    - Enumerations: `e_` + base type name
    - Structs: `st_` + base type name
    - Unions: `u_` + base type name
1. Anonymous structures are not allowed within defined types.

### 4.7.4. Enumerations
1. Enumeration values shall be all-caps and include only alphanumeric characters and underscores (`_`).
1. Enumeration values shall start with the base name of the type (i.e. excluding `_t`).
1. Enumerations should only contain valid values. Ending with a "\_MAX" or "\_END" value should be avoided wherever possible.

### 4.7.5. Structures and Unions
1. Structure and union members shall follow the same naming rules as local variables and function pointers.

### 4.7.6. Variables and Constants
1. Variables and constants shall have a short name that is descriptive of their purpose, includes only lowercase alphanumeric characters and underscores, and is no fewer than three characters long.
1. For-loop variables may have a name shorter than three characters.
1. The following prefixes shall be used when applicable:

    | Prefix | Use | Example |
    | ------ | --- | ------- |
    | `p_` | Pointer | `static uint8_t * p_data` |
    | `pp_` | Pointer to a pointer | `static uint32_t ** pp_data_table` |
    | `g_` | Global | `uint32_t g_error_flags` |
    | `gp_` | Global pointer | `uint32_t * gp_vector_table` |

1. Where units (e.g. MHz, ms) are important, they should be clearly specified in the name.
1. Function pointers ignore the above rules and shall be named in lower camel case (e.g. `uint32_t (* dutyCycleSet)(...)`)

### 4.7.7. Functions
1. Public function names shall be composed in the following order:
    1. The prefix `R_` for hardware drivers or `RM_` for middleware
        - This prefix shall match the enclosing file.
    2. One of the following in all-caps followed by an underscore:
        - The abbreviated name of the hardware or peripheral block provided in the device manual (e.g. `ADC_`)
        - A short version of the middleware name (e.g. `MOTOR_DRIVER_`)
    3. The function name in upper camel case (e.g. `DutyCycleSet`)
1. Private function names shall be all lowercase with underscores between words starting with the same module or middleware name used in public functions.
1. Interrupt service routine names shall end in `_isr` (e.g. `void adc_scan_end_isr(void)`).
1. Functions representing threads should have a name ending with `_thread` (e.g. `void alarm_thread(...)`).

## 4.8. Dead Code
1. Code in public files that is not used in any configuration shall be removed.
1. Comments shall not be used to disable code. Use the preprocessor (`#if 0`, etc.) to temporarily disable a block of code.
1. Debug code should not be present in public files and regardless of location should be disabled by default with an `#if` or `#ifdef`.
1. Test code that is not used in any configuration should be removed. If such code is kept, justification shall be provided in a comment at the top of the block or at the definition of the relevant macro.

# 5. Code

## 5.1. Signed Integers
1. Bit fields shall not be defined within signed integer types.
1. Bitwise operations shall not be used to manipulate signed integer data.
1. Signed and unsigned integers shall never be combined within comparisons or expressions.

## 5.2. Floating Point
1. If the system the code will run on does not have floating point hardware then do not use floating point constants, variables, or operations unless absolutely necessary.
1. If the system supports only single-precision, do not use double-precision unless absolutely necessary.
1. All single-precision literals shall have the suffix "f" (e.g. `3.1415927f`).
1. Never test floating-point values for equality (`==`).

## 5.3. Function-Like Macros
1. Parameterized macros shall not be used if an inline function can accomplish the same task.
1. If a parameterized macro is used, the following rules apply:
    1. Parentheses shall surround the entire macro body
    2. Parentheses shall surround each individual parameter use
    3. Parameters should be used only once to avoid unintended side effects.

## 5.4. Structs and Unions
1. Appropriate care shall be taken to prevent the compiler from inserting unnecessary padding bytes within structures and unions, e.g.:
    - Manually padding to match the layout of peripheral registers or a communications packet format
    - Ordering elements of different types to avoid padding due to alignment
    - Using the `packed` attribute
1. Appropriate care shall be taken to prevent the compiler from altering the intended order of the bits within bit fields.
1. Except where required, type alignment shall not be violated to eliminate padding.

## 5.5. Variables
1. Variables shall be declared using the most efficient type for the architecture unless a different type is required.
    1. Parameters for functions that are part of a cross-platform API shall assume 32-bit architecture.
1. All variables shall be initialized before being read.
1. Variables with file-scope shall be declared `static`.
1. The comma (`,`) operator shall not be used in variable declarations. Each variable shall be declared on its own line.
1. Variables inside functions should be declared as needed rather than all at the top.

## 5.6. Magic Numbers
1. Integer literals outside the range 0 to 31 (inclusive) shall be declared as a macro.

## 5.7. Arrays
1. Variable-Length Arrays (VLAs) shall not be used.

## 5.8. Functions
1. All functions shall have a defined prototype.
1. The prototype for public functions shall be located in the header file of the same name.
1. Private functions shall be defined `static`.
1. Parameters and return type shall be explicitly defined.
1. Functions shall contain exclusively C code or inline assembly, never a combination of the two.
1. Within functions, code shall not be nested more than four levels.
    - Use the following techniques to reduce complexity and aid understanding:
        - Extract significant or repeated code blocks into discrete functions
        - Handle error conditions first to avoid nested if-checks
1. Functions with a non-`void` return type shall have an explicit return statement.
1. If the hardware or software architecture allows for any unexpected or otherwise unhandled interrupts to be fired a stub or default ISR should be installed in the vector table at their location.

## 5.9. Assignments (`=`)
1. Except in the initialization of a `for` loop, assignments shall be their own statement and not part of any other statement.

## 5.10. Equivalence Tests (`==`)
1. When evaluating the equality of a variable with a constant value, place the constant value on the left side of the comparison operator.
    - This prevents hard-to-detect bugs when accidentally using `=` instead of `==`.

## 5.11. If-Else Statements
1. Any `if` statement with an `else if` clause shall end with an `else` clause.

## 5.12. Ternary Statements (`a ? b : c`)
1. Ternary statements shall not be nested more than one level.
    - Use additional statements to reduce complexity and aid understanding.

## 5.13. Switch Statements
1. All `switch` statements shall include a `default` block.
1. Any case that intentionally allows the execution to continue ("drop through") to the next case shall be identified with an appropriate comment.
1. Multiple cases that use one code block should be identified with one comment at the top to state that the following cases are all intentionally common.

## 5.14. Keywords

### 5.14.1. Declarations

1. All functions and variables that do not need to be visible outside the module shall be declared `static`.
1. `const` shall be used in declarations wherever appropriate, including:
    - Variables that are not changed after initialization
    - Function parameters that should not be modified (e.g. `uint8_t * const p_data`)
    - Read-only struct/union fields (e.g. undefined peripheral register regions)
    - Strongly-typed alternative to `#define`
1. `volatile` should be used in declarations wherever appropriate, including:
    - Global variables accessed in an interrupt context
    - Global variables accessed by multiple tasks
    - Pointers to memory-mapped registers
    - Pointers to shared memory
    - Delay loop counters (to prevent the delay being optimized out)

4. Use of `char` is restricted to the declaration and use of string data.

### 5.14.2. Unconditional Jumps (`break`, `continue`, `goto`)

Unconditional jumps shall be used only when they are more clear, easy to understand and efficient than alternative solutions.

1. `break` shall be used to terminate cases within switch statements.
1. `goto` shall only be used to create a single point-of-return at the end of the enclosing function or nested loop.
1. `goto` shall always jump forwards (down), never backwards (up).
1. Labels shall start with the name of the enclosing function.

### 5.14.3. Other Keywords

1. The `auto` keyword is obsolete and shall not be used.
1. The `register` keyword is virtually obsolete and shall not be used.

# 6. Documentation

## 6.1. Language
1. All documentation shall be provided in English and adhere to the following:
    - Complete sentences (not strictly required for comments in code blocks)
    - Correct capitalization
    - Proper spelling
    - Appropriate punctuation
    - Professional language

## 6.2. File Headers

### 6.2.1. Disclaimer

1. All public source and header files shall be internally maintained with a disclaimer placeholder similar to the below:
    ```c
    /* ${DISCLAIMER_PLACEHOLDER} */
    ```
1. The disclaimer placeholder shall be replaced with the relevant disclaimer in all files before any public release.

### 6.2.2. Doxygen
1. The contents of documented header and source files after the disclaimer should be added to a relevant Doxygen group like below:
    ```c
    /*******************************************************************************************************************//**
    * @addtogroup GROUP_NAME
    * @{
    **********************************************************************************************************************/

    /* ... (all file contents here) */

    /*******************************************************************************************************************//**
    * @} (end addtogroup GROUP_NAME)
    **********************************************************************************************************************/
    ```


## 6.3. Function and Type Headers
1. Public function definitions should use the following header format:
    ```c
    /*******************************************************************************************************************//**
    * {Brief description of function.}
    *
    * {Optional detailed function information.}
    *
    * @param[in]  {input_parameter}       {Decription of input parameter}
    * @param[out] {output_parameter}      {Decription of output parameter}
    *
    * @retval {SUCCESS_RETURN_VALUE}      {Successful return value description}
    * @retval {FAILURE_RETURN_VALUE}      {Failure return value description}
    **********************************************************************************************************************/
    ```
1. The following additional Doxygen comment markers may be used to highlight important information:
    - `@warning` should be used to alert users to requirements or behavior that might be unexpected.
    - `@note` may be used to provide additional context, suggestions, and/or any assumptions made.
    - `@see [symbol]` adds a linked reference to `[symbol]`.
    - `::` may be used to link to an element in a struct or union type (e.g. `union_t::element`).

## 6.4. Code Comments

1. Code comments should describe effect, not action.  In other words, comments should describe what happens as a result of execution and/or why, not repeat the code literally. `/* Set TPSC bits */` is not as helpful to users as `/* Set clock divisor to divide by 64 */`.
    - Avoid explaining the obvious; assume the reader understands C. Comments like `/* Shift left 2 bits */` are unhelpful and redundant.
1. Avoid end-of-line comments unless the following conditions are met:
    - The line is unclear or confusing
    - It is part of a logical code block and would not make sense to isolate
    - A very short comment will suffice
1. End-of-line comments may be used to add a brief explanation of struct elements, union elements, and macro definitions.
1. Unless the function is obvious, each code block within a function should have a brief comment.
    - The number and length of code blocks and comments should generally be proportional to the complexity of the related code.
1. Nontrivial casts should have a comment to explain how and/or why the values map correctly to the new type.
1. Any assumptions made in the code should be clearly explained in comments.

## 6.5. External References
1. When algorithms or technical details from a published document or page are used a comment shall be added to point the user to the source (including version number if applicable).
1. Flowcharts and diagrams should be added to auxiliary documentation and referred to instead of drawn out within a comment.