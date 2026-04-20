# Lexical Analysis Reference

## Overview

**Lexical analysis** (also called tokenization or scanning) is the first phase of compilation. It transforms source code characters into a stream of **tokens** - meaningful units like keywords, identifiers, operators, and literals.

### Purpose

- Input: Raw source code (string of characters)
- Output: Token stream with type and value information
- Handles: Comments, whitespace, escape sequences, error recovery

## Token Types

### 1. Keywords

Reserved words that have special meaning in the language.

| Keyword | Purpose | Example |
|---------|---------|---------|
| `piece` | Integer type | `piece of count = 10;` |
| `chars` | String type | `chars of name = "Alice";` |
| `flag` | Boolean type | `flag of ready = up;` |
| `sip` | Float type | `sip of weight = 3.14;` |
| `table` | Table/struct definition | `table of Student = [...]` |
| `of` | Type separator | `piece of x` |
| `prepare` | Function definition | `prepare piece of add()` |
| `start` | Entry point | `start() { ... }` |
| `serve` | Return statement | `serve result;` |
| `check` | If condition | `check (x > 0) { ... }` |
| `alt` | Else-if condition | `alt (x < 0) { ... }` |
| `instead` | Else condition | `instead { ... }` |
| `menu` | Switch statement | `menu(day) { ... }` |
| `choice` | Case in switch | `choice 1: ...` |
| `usual` | Default case | `usual: ...` |
| `pass` | For loop | `pass (i=0; i<10; i++) { ... }` |
| `repeat` | Loop control | `repeat(condition);` |
| `order` | Do-while block | `order { ... } repeat(i > 0);` |
| `stop` | Break statement | `stop;` |
| `next` | Continue statement | `next;` |
| `bill` | Print function | `bill("Hello");` |
| `up` | Boolean true | `flag of ready = up;` |
| `down` | Boolean false | `flag of ready = down;` |

### 2. Identifiers

Custom names for variables, functions, tables, etc.

**Rules:**
- Start with letter (a-z, A-Z) or underscore (_)
- Followed by letters, digits (0-9), or underscores
- Case-sensitive: `count`, `Count`, `COUNT` are different
- Cannot be keywords

**Valid Examples:**
```
myVariable
_privateVar
count123
Student
add_numbers
MAX_SIZE
```

**Invalid Examples:**
```
123count       // Starts with digit
my-var         // Contains hyphen
if             // Is a keyword
my var         // Contains space
```

### 3. Literals

#### Integer Literals
Whole numbers without decimal point.

**Format:** `[+-]?[0-9]+`

**Examples:**
```
0
42
-15
999
```

#### String Literals
Text enclosed in double quotes.

**Format:** `".*"` (characters between double quotes)

**Escape Sequences:**
- `\"` - Double quote
- `\\` - Backslash
- `\n` - Newline (in source, represents newline character)
- `\t` - Tab
- `\r` - Carriage return

**Examples:**
```platter
"Hello"
"Hello, World!"
"Line 1\nLine 2"
"Path: C:\\Users\\Name"
```

#### Float Literals
Decimal numbers.

**Format:** `[+-]?[0-9]+\.[0-9]+`

**Examples:**
```
3.14
0.5
-2.718
99.99
```

#### Boolean Literals
The keywords `up` and `down` represent true and false.

```
up      // Boolean true
down    // Boolean false
```

### 4. Operators

Single and multi-character operators.

#### Arithmetic Operators
```
+       // Addition
-       // Subtraction
*       // Multiplication
/       // Division
%       // Modulo
```

#### Relational Operators
```
<       // Less than
>       // Greater than
<=      // Less than or equal
>=      // Greater than or equal
==      // Equal
!=      // Not equal
```

#### Logical Operators
```
&&      // Logical AND
||      // Logical OR
!       // Logical NOT
```

#### Assignment Operators
```
=       // Simple assignment
+=      // Add and assign
-=      // Subtract and assign
*=      // Multiply and assign
/=      // Divide and assign
%=      // Modulo and assign
```

### 5. Punctuation

Structural tokens.

```
(       // Left paren
)       // Right paren
{       // Left brace
}       // Right brace
[       // Left bracket
]       // Right bracket
;       // Semicolon
,       // Comma
.       // Dot (for field access)
```

### 6. Comments

Text that the compiler ignores.

#### Single-Line Comments
Start with `//` and continue to end of line.

```platter
piece of x = 5;  // This is a comment
// Entire line is a comment
bill(x);
```

#### Multi-Line Comments
Enclosed in `/* ... */`.

```platter
/*
  This is a multi-line comment.
  It can span multiple lines.
  Everything here is ignored.
*/
piece of x = 5;
```

**Rules:**
- Cannot be nested
- Must be properly closed with `*/`

### 7. Whitespace

Space, tab, and newline characters are generally ignored.

**Types:**
- Space: ` ` (ASCII 32)
- Tab: `\t` (ASCII 9)
- Newline: `\n` (ASCII 10)
- Carriage return: `\r` (ASCII 13)

**Purpose:** Separate tokens and improve readability

**Example:**
```platter
piece of x=5;      // Valid (no spaces)
piece of x = 5;    // Valid (with spaces)
piece of
      x = 5;       // Valid (newlines are ignored)
```

## Lexical Error Handling

Errors detected during tokenization:

### 1. Unclosed Comments
Multi-line comment not properly terminated.

```platter
/*
  This comment never closes
piece of x = 5;
```

**Error:** "Unclosed comment"

### 2. Unclosed String
String literal not terminated with closing quote.

```platter
piece of msg = "This string never closes;
bill(msg);
```

**Error:** "Unclosed string literal"

### 3. Invalid Character
Character not recognized by the lexer.

```platter
piece of x = 5 @ 3;  // @ is not valid
```

**Error:** "Invalid character: @"

### 4. Malformed Number
Invalid numeric literal format.

```platter
piece of x = 123.45.67;  // Two decimal points
piece of x = 0x1F;       // Hexadecimal not supported
```

## Token Stream Example

### Source Code
```platter
piece of count = 10;
pass (count; count > 0; count -= 1) {
    bill(count);
}
```

### Token Stream

| Token Type | Token Value | Line | Column |
|-----------|------------|------|--------|
| keyword | piece | 1 | 1 |
| keyword | of | 1 | 6 |
| id | count | 1 | 9 |
| op | = | 1 | 15 |
| number | 10 | 1 | 17 |
| punc | ; | 1 | 19 |
| keyword | pass | 2 | 1 |
| punc | ( | 2 | 5 |
| id | count | 2 | 6 |
| punc | ; | 2 | 11 |
| id | count | 2 | 13 |
| op | > | 2 | 19 |
| number | 0 | 2 | 21 |
| punc | ; | 2 | 22 |
| id | count | 2 | 24 |
| op | -= | 2 | 30 |
| number | 1 | 2 | 33 |
| punc | ) | 2 | 34 |
| punc | { | 2 | 36 |
| keyword | bill | 3 | 5 |
| punc | ( | 3 | 9 |
| id | count | 3 | 10 |
| punc | ) | 3 | 15 |
| punc | ; | 3 | 16 |
| punc | } | 4 | 1 |

## Lexer Implementation Details

### State Machine

The Platter lexer uses a Deterministic Finite Automaton (DFA) to recognize tokens:

```
START
 ├─ [a-zA-Z_]  →  IDENTIFIER/KEYWORD
 ├─ [0-9]       →  NUMBER
 ├─ [+-]        →  OP_START
 ├─ [*/]        →  ARITHMETIC_OP
 ├─ [%]         →  MOD_OP
 ├─ [<>=!]      →  RELATIONAL_OP
 ├─ [=]         →  ASSIGNMENT_OP
 ├─ [&|]        →  LOGICAL_OP
 ├─ [(){}[\];,.]→  PUNCTUATION
 ├─ ["]         →  STRING_START
 ├─ [/]         →  COMMENT_OR_DIVIDE
 ├─ [ \t\n\r]  →  WHITESPACE
 └─ [other]     →  ERROR
```

### Multi-Character Operators

Some operators consist of multiple characters:

```
->  Recognized as: - (minus) followed by > (greater)
    OR if lexer supports: -= (compound assignment)
    
<=  Two-character operator (less-than-equal)
>=  Two-character operator (greater-than-equal)
==  Two-character operator (equality)
!=  Two-character operator (inequality)
&&  Two-character operator (logical AND)
||  Two-character operator (logical OR)
+=  Two-character operator (add-assign)
-=  Two-character operator (subtract-assign)
*=  Two-character operator (multiply-assign)
/=  Two-character operator (divide-assign)
%=  Two-character operator (modulo-assign)
```

### Keyword Recognition

Keywords are typically stored in a **reserved word table**:

```python
RESERVED_WORDS = {
    'piece': Token.KEYWORD,
    'chars': Token.KEYWORD,
    'flag': Token.KEYWORD,
    'prepare': Token.KEYWORD,
    'start': Token.KEYWORD,
    'bill': Token.KEYWORD,
    # ... etc
}

# When encountering an identifier, check if it's in the table
if identifier_name in RESERVED_WORDS:
    return Token.KEYWORD
else:
    return Token.IDENTIFIER
```

## Practical Examples

### Example 1: Simple Declaration

**Source:**
```platter
piece of x = 42;
```

**Tokens:**
```
[KEYWORD(piece), KEYWORD(of), ID(x), OP(=), NUMBER(42), PUNC(;)]
```

### Example 2: Function with Comment

**Source:**
```platter
// Calculate sum
prepare piece of sum(piece of a) {
    serve a + 5;
}
```

**Tokens:**
```
[KEYWORD(prepare), KEYWORD(piece), KEYWORD(of), ID(sum),
 PUNC((), KEYWORD(piece), KEYWORD(of), ID(a), PUNC()),
 PUNC({), KEYWORD(serve), ID(a), OP(+), NUMBER(5), PUNC(;), PUNC(})]
```

### Example 3: String with Escape

**Source:**
```platter
chars of msg = "Hello\\nWorld";
```

**Tokens:**
```
[KEYWORD(chars), KEYWORD(of), ID(msg), OP(=), 
 STRING("Hello\\nWorld"), PUNC(;)]
```

---

See also:
- [Grammar Specification](GRAMMAR.md)
- [AST & Parse Tree Guide](AST_GUIDE.md)
- [Platter Language User Guide](../README.md)
