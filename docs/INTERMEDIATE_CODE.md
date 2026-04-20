# Intermediate Code Generation

## Overview

The Platter compiler uses **Three-Address Code (TAC)** as its intermediate representation (IR). TAC is a low-level, pseudo-code representation where each instruction has at most three operands.

### Purpose

Intermediate code serves as a bridge between:
- **High-level**: Platter source code with complex expressions
- **Low-level**: Machine code or virtual machine instructions

Benefits:
- Enables compiler optimization
- Simplifies code generation
- Makes the compiler portable across different backends
- Facilitates debugging and analysis

## Three-Address Code (TAC) Instructions

TAC instructions have the general form:
```
result = arg1 op arg2
```

Each instruction performs one operation with at most three addresses (variables/locations).

### Instruction Types

#### 1. Assignment (ASSIGN)
Assigns a value to a variable.

```
result = arg1
```

**TAC Example:**
```
t1 = 42
x = y
```

#### 2. Binary Operation (BINOP)
Performs arithmetic, logical, or relational operations.

```
result = arg1 op arg2
```

**Supported Operators:**
- Arithmetic: `+`, `-`, `*`, `/`, `%`
- Relational: `<`, `>`, `<=`, `>=`, `==`, `!=`
- Logical: `&&`, `||`

**TAC Examples:**
```
t1 = a + b
t2 = x * 2
t3 = flag && up
result = count > 10
```

#### 3. Unary Operation (UNOP)
Performs unary operations (negation, logical NOT, etc.).

```
result = op arg1
```

**TAC Example:**
```
t1 = -x
t2 = !flag
```

#### 4. Array Access (ARRAY_READ)
Reads a value from an array.

```
result = array[index]
```

**TAC Example:**
```
t1 = numbers[0]
t2 = matrix[i][j]
```

#### 5. Array Assignment (ARRAY_WRITE)
Assigns a value to an array element.

```
array[index] = value
```

**TAC Example:**
```
numbers[0] = 42
matrix[i][j] = temp
```

#### 6. Table Field Access (TABLE_READ)
Reads a field from a table (struct).

```
result = table.field
```

**TAC Example:**
```
t1 = student.name
t2 = book.year
```

#### 7. Table Field Assignment (TABLE_WRITE)
Assigns a value to a table field.

```
table.field = value
```

**TAC Example:**
```
student.age = 21
book.author = "Author Name"
```

#### 8. Label (LABEL)
Marks a position in code for jumps.

```
LABEL: target
```

**TAC Example:**
```
LABEL: loop_start
LABEL: exit_loop
```

#### 9. Unconditional Jump (GOTO)
Jumps to a labeled location.

```
GOTO target
```

**TAC Example:**
```
GOTO loop_start
GOTO exit_block
```

#### 10. Conditional Jump (CJUMP)
Jumps based on a condition.

```
IF condition GOTO target
IFNOT condition GOTO target
```

**TAC Example:**
```
IF x > 0 GOTO positive_block
IFNOT flag GOTO else_block
```

#### 11. Function Call (CALL)
Calls a function/recipe with parameters.

```
result = CALL function_name(arg1, arg2, ...)
```

**TAC Example:**
```
t1 = CALL addNumbers(5, 3)
t2 = CALL fibonacci(10)
```

#### 12. Function Parameter (PARAM)
Specifies function call parameters.

```
PARAM arg1
PARAM arg2
```

**TAC Example:**
```
PARAM x
PARAM y
t1 = CALL maximum(x, y)
```

#### 13. Return Statement (RETURN)
Returns from a function.

```
RETURN value
```

**TAC Example:**
```
RETURN 42
RETURN result
```

#### 14. Function Begin/End (FUNCTION_BEGIN, FUNCTION_END)
Marks function boundaries.

```
FUNCTION_BEGIN function_name
FUNCTION_END function_name
```

#### 15. Output Operation (CALL bill)
Outputs values to console.

```
PARAM value
CALL bill()
```

#### 16. Comment (COMMENT)
Provides documentation in the IR.

```
COMMENT: Description
```

## Example: Expression to TAC

### Platter Source Code
```platter
prepare piece of calculate(piece of a, piece of b) {
    piece of result = (a + b) * 2;
    serve result;
}
```

### Generated TAC
```
FUNCTION_BEGIN calculate
    t1 = a + b        // Binary operation
    t2 = t1 * 2       // Binary operation
    result = t2       // Assignment
    RETURN result     // Return
FUNCTION_END calculate
```

## Example: Control Flow to TAC

### Platter Source Code
```platter
start() {
    piece of x = 10;
    check (x > 5) {
        bill("Greater than 5");
    } instead {
        bill("Less than or equal to 5");
    }
}
```

### Generated TAC
```
FUNCTION_BEGIN start
    x = 10                           // Assignment
    t1 = x > 5                       // Relational operation
    IF t1 GOTO true_block           // Conditional jump
    
    PARAM "Less than or equal to 5"  // Else block
    CALL bill()
    GOTO exit_block
    
LABEL: true_block
    PARAM "Greater than 5"           // If block
    CALL bill()
    
LABEL: exit_block
FUNCTION_END start
```

## Temporary Variables

The compiler generates temporary variables (often named `t1`, `t2`, etc.) to hold intermediate results:

- **Purpose**: Break down complex expressions into simple, three-address operations
- **Naming**: Usually `t0`, `t1`, `t2`, ...
- **Lifetime**: Limited to the scope where needed

### Example
```platter
piece of result = (x + y) * (z - w);
```

**Breaks down to:**
```
t1 = x + y
t2 = z - w
t3 = t1 * t2
result = t3
```

## Optimization Passes

After TAC generation, several optimization passes improve code efficiency:

### 1. Constant Folding
Pre-computes operations on constants.

**Before:**
```
t1 = 5 + 3
x = t1
```

**After:**
```
x = 8
```

### 2. Dead Code Elimination
Removes unused variable assignments.

**Before:**
```
t1 = x + y
t2 = 42
result = t2
```

**After:**
```
result = 42
```

### 3. Copy Propagation
Eliminates redundant assignments.

**Before:**
```
t1 = x
t2 = t1
x = t2
```

**After:**
```
x = x
```

### 4. Constant Propagation
Replaces variables with their known constant values.

**Before:**
```
x = 5
t1 = x + 3
```

**After:**
```
x = 5
t1 = 8
```

### 5. Strength Reduction
Replaces expensive operations with cheaper ones.

**Before:**
```
t1 = x * 2
```

**After:**
```
t1 = x + x
```

### 6. Algebraic Simplification
Simplifies algebraic expressions.

**Before:**
```
t1 = x + 0
t2 = y * 1
t3 = z * 0
```

**After:**
```
t1 = x
t2 = y
t3 = 0
```

## IR Interpreter

The compiler includes a TAC interpreter (TACInterpreter) that can:
- Execute TAC instructions
- Evaluate expressions
- Validate generated code
- Debug intermediate representation

---

See also:
- [Compiler Architecture](../platter-compiler-sveltejs/README.md)
- [AST & Parse Tree Guide](AST_GUIDE.md)
- [Grammar Specification](GRAMMAR.md)
