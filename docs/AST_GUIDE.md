# Abstract Syntax Tree (AST) and Parse Tree Guide

## Overview

An **Abstract Syntax Tree (AST)** is a tree representation of the syntactic structure of source code. It abstracts away unnecessary details like parentheses and semicolons, capturing only the essential structure.

### Key Concepts

- **AST Nodes**: Represent language constructs (variables, functions, expressions, statements)
- **Parent-Child Relationships**: Show hierarchical composition of language elements
- **Type Information**: Nodes store semantic information about types, scope, and meaning

## AST Node Types

### Program Structure

#### Program Node
Root of the entire AST, representing one complete Platter program.

```
Program
├── global_decl: List[VarDecl | TableDecl]
├── recipe_decl: List[RecipeDecl]
└── start_platter: Statement (start() block)
```

**Properties:**
- `global_decl`: All global variable declarations
- `recipe_decl`: All function/recipe declarations
- `start_platter`: The main `start()` block containing the entry point code

### Variable Declarations

#### VarDecl (Variable Declaration)
Represents a variable declaration with optional initialization.

```
VarDecl(
    name: str,
    type: str,           # "piece", "chars", "flag", "sip"
    dimensions: List[int],  # [] for scalar, [n] for 1D array, etc.
    initializer: Optional[Expr],
    is_global: bool
)
```

**Example:**
```platter
piece of count = 10;
chars[] of names = ["Alice", "Bob"];
```

Generates:
```
VarDecl("count", "piece", [], Literal(10), True)
VarDecl("names", "chars", [2], ArrayLiteral([...]), True)
```

#### TableDecl (Table/Struct Declaration)
Represents a custom table type definition.

```
TableDecl(
    name: str,
    fields: List[VarDecl]  # Field declarations
)
```

**Example:**
```platter
table of Student = [
    chars of name;
    piece of age;
];
```

Generates:
```
TableDecl("Student", [
    VarDecl("name", "chars", []),
    VarDecl("age", "piece", [])
])
```

### Function Declarations

#### RecipeDecl (Function Declaration)
Represents a function/recipe definition.

```
RecipeDecl(
    return_type: str,     # "piece", "chars", "flag", "sip", or table name
    name: str,
    parameters: List[Parameter],
    body: Statement
)
```

**Example:**
```platter
prepare piece of add(piece of a, piece of b) {
    serve a + b;
}
```

Generates:
```
RecipeDecl(
    return_type="piece",
    name="add",
    parameters=[Parameter("a", "piece"), Parameter("b", "piece")],
    body=Block([ReturnStmt(BinaryOp("+", Var("a"), Var("b")))])
)
```

#### Parameter
Represents a function parameter.

```
Parameter(
    name: str,
    type: str,
    dimensions: List[int]  # For array parameters
)
```

### Expressions

#### Literal
Represents a constant value.

```
Literal(
    type: str,    # "piece", "chars", "flag", "sip"
    value: Any
)
```

**Examples:**
```
Literal("piece", 42)        // 42
Literal("chars", "hello")   // "hello"
Literal("flag", True)       // up
Literal("sip", 3.14)        // 3.14
```

#### BinaryOp
Represents a binary operation.

```
BinaryOp(
    operator: str,  # "+", "-", "*", "/", "%", "<", ">", "==", etc.
    left: Expr,
    right: Expr
)
```

**Example:**
```
BinaryOp("+", Var("x"), Literal("piece", 5))
// Represents: x + 5
```

#### UnaryOp
Represents a unary operation.

```
UnaryOp(
    operator: str,  # "-", "!"
    operand: Expr
)
```

**Example:**
```
UnaryOp("!", Var("flag"))
// Represents: !flag
```

#### Variable Reference
Represents a reference to a variable.

```
Var(
    name: str,
    type: Optional[str]  # Type info from symbol table
)
```

**Example:**
```
Var("count")  // Reference to variable 'count'
```

#### FunctionCall
Represents a call to a function/recipe.

```
FunctionCall(
    name: str,
    arguments: List[Expr]
)
```

**Example:**
```platter
calculate(a, b)
```

Generates:
```
FunctionCall("calculate", [Var("a"), Var("b")])
```

#### ArrayAccess
Represents array element access.

```
ArrayAccess(
    array: Expr,
    indices: List[Expr]  # List for multi-dimensional arrays
)
```

**Example:**
```platter
numbers[0]
matrix[i][j]
```

Generates:
```
ArrayAccess(Var("numbers"), [Literal("piece", 0)])
ArrayAccess(Var("matrix"), [Var("i"), Var("j")])
```

#### TableAccess (Field Access)
Represents accessing a field of a table/struct.

```
TableAccess(
    table: Expr,
    field: str
)
```

**Example:**
```platter
student.name
book.author
```

Generates:
```
TableAccess(Var("student"), "name")
TableAccess(Var("book"), "author")
```

#### ArrayLiteral
Represents an array literal.

```
ArrayLiteral(
    elements: List[Union[Expr, ArrayLiteral]],
    bases_type: str,
    dimensions: List[int]
)
```

**Example:**
```platter
[1, 2, 3]
[[1, 2], [3, 4]]
```

Generates:
```
ArrayLiteral([Literal("piece", 1), Literal("piece", 2), Literal("piece", 3)], "piece", [3])
```

#### TableLiteral
Represents a table/struct literal.

```
TableLiteral(
    type_name: str,
    fields: Dict[str, Expr]
)
```

**Example:**
```platter
[
    name = "Alice";
    age = 20;
]
```

Generates:
```
TableLiteral("Student", {
    "name": Literal("chars", "Alice"),
    "age": Literal("piece", 20)
})
```

### Statements

#### Block
Sequence of statements.

```
Block(
    statements: List[Statement]
)
```

#### Assignment
Assigns a value to a variable.

```
Assignment(
    target: Expr,  # Var, ArrayAccess, or TableAccess
    value: Expr
)
```

**Examples:**
```platter
x = 5;
numbers[0] = 42;
student.age = 21;
```

#### CompoundAssignment
Compound assignment operators (+=, -=, etc.).

```
CompoundAssignment(
    operator: str,  # "+=", "-=", "*=", "/=", "%="
    target: Expr,
    value: Expr
)
```

**Example:**
```platter
count += 1;
```

Generates:
```
CompoundAssignment("+=", Var("count"), Literal("piece", 1))
```

#### IfStatement
Represents if/else-if/else conditionals.

```
IfStatement(
    condition: Expr,
    then_block: Statement,
    else_if_blocks: List[Tuple[Expr, Statement]],
    else_block: Optional[Statement]
)
```

**Example:**
```platter
check (x > 0) {
    // positive
} alt (x < 0) {
    // negative
} instead {
    // zero
}
```

#### SwitchStatement
Represents switch/menu statements.

```
SwitchStatement(
    expression: Expr,
    cases: List[Case],
    default_case: Optional[Statement]
)
```

Where `Case` is:
```
Case(
    value: Expr,
    body: Statement
)
```

**Example:**
```platter
menu (day) {
    choice 1:
        bill("Monday");
    choice 2:
        bill("Tuesday");
    usual:
        bill("Other");
}
```

#### ForLoop
Represents for loops (pass).

```
ForLoop(
    init: Optional[Statement],
    condition: Optional[Expr],
    update: Optional[Statement],
    body: Statement
)
```

**Example:**
```platter
pass (piece of i = 0; i < 10; i += 1) {
    // body
}
```

#### DoWhileLoop
Represents do-while loops (order/repeat).

```
DoWhileLoop(
    body: Statement,
    condition: Expr
)
```

**Example:**
```platter
order {
    // body
} repeat (count > 0);
```

#### FunctionCallStatement
Function call as a statement.

```
FunctionCallStatement(
    call: FunctionCall
)
```

**Example:**
```platter
bill("Hello");
```

#### ReturnStatement
Represents return from function.

```
ReturnStatement(
    value: Optional[Expr]
)
```

**Example:**
```platter
serve result;
serve 42;
```

#### BreakStatement
Exits from a loop.

```
BreakStatement()
```

**Example:**
```platter
stop;
```

#### ContinueStatement
Continues to next iteration.

```
ContinueStatement()
```

**Example:**
```platter
next;
```

## Example: Complete AST

### Platter Source Code

```platter
piece of globalCount = 0;

prepare piece of increment(piece of x) {
    piece of result = x + 1;
    serve result;
}

start() {
    piece of a = 5;
    piece of b = increment(a);
    check (b > 10) {
        bill("Greater than 10");
    } instead {
        bill("Less than or equal to 10");
    }
}
```

### Generated AST Structure

```
Program
├── global_decl
│   └── VarDecl("globalCount", "piece", [], Literal(0), True)
├── recipe_decl
│   └── RecipeDecl
│       ├── return_type: "piece"
│       ├── name: "increment"
│       ├── parameters: [Parameter("x", "piece")]
│       └── body: Block([
│           │   VarDecl("result", "piece", [], None, False),
│           │   Assignment(Var("result"), BinaryOp("+", Var("x"), Literal(1))),
│           │   ReturnStatement(Var("result"))
│           │])
└── start_platter: Block([
    │   VarDecl("a", "piece", [], Literal(5), False),
    │   VarDecl("b", "piece", [], FunctionCall("increment", [Var("a")]), False),
    │   IfStatement(
    │       ├── condition: BinaryOp(">", Var("b"), Literal(10)),
    │       ├── then_block: Block([FunctionCallStatement(FunctionCall("bill", [Literal("Greater than 10")]))]),
    │       └── else_block: Block([FunctionCallStatement(FunctionCall("bill", [Literal("Less than or equal to 10")]))])
    │   )
    │])
```

## AST Traversal

The compiler processes the AST through multiple passes:

1. **Semantic Analysis**: Walk the AST to build symbol tables and check types
2. **Intermediate Code Generation**: Traverse AST to generate TAC instructions
3. **Optimization**: Walk optimized AST for dead code and constant analysis

### Visitor Pattern

Common implementation pattern:

```python
class ASTVisitor:
    def visit_program(self, node: Program):
        # Process program
        pass
    
    def visit_var_decl(self, node: VarDecl):
        # Process variable declaration
        pass
    
    def visit_binary_op(self, node: BinaryOp):
        # Process binary operation
        pass
```

---

See also:
- [Grammar Specification](GRAMMAR.md)
- [Intermediate Code Generation](INTERMEDIATE_CODE.md)
- [Platter Language User Guide](../README.md)
