# Grammar Specification

## Overview

This document describes the **context-free grammar** of the Platter programming language. The grammar defines the syntactic rules that determine what constitutes valid Platter source code.

### Grammar Notation

We use **Extended Backus-Naur Form (EBNF)** notation:

- `|` - Alternative (OR)
- `*` - Zero or more repetitions
- `+` - One or more repetitions
- `?` - Optional (zero or one)
- `()` - Grouping
- `""` - Literal terminal
- `[]` - Character class

### Precedence and Associativity

| Precedence | Operator | Associativity | Type |
|-----------|----------|---------------|------|
| Highest | `()` `[]` `.` | Left | Postfix |
| | `!` `-` (unary) | Right | Prefix |
| | `*` `/` `%` | Left | Multiplicative |
| | `+` `-` | Left | Additive |
| | `<` `>` `<=` `>=` | Left | Relational |
| | `==` `!=` | Left | Equality |
| | `&&` | Left | Logical AND |
| Lowest | `\|\|` | Left | Logical OR |

## Root Production

```ebnf
<program> ::= <global_decl>* <recipe_decl>* <start_block>

<start_block> ::= "start" "(" ")" "{" <statement>* "}"
```

## Global Declarations

### Global Variable Declarations

```ebnf
<global_decl> ::= <piece_global_decl>
                | <chars_global_decl>
                | <flag_global_decl>
                | <sip_global_decl>
                | <table_decl>

<piece_global_decl> ::= "piece" <piece_decl> ";"
<chars_global_decl> ::= "chars" <chars_decl> ";"
<flag_global_decl> ::= "flag" <flag_decl> ";"
<sip_global_decl> ::= "sip" <sip_decl> ";"

<piece_decl> ::= "of" <piece_id> (<"," <piece_id>)*
<chars_decl> ::= ("[]")? "of" <chars_id> (<"," <chars_id>)*
<flag_decl> ::= <flag_id> (<"," <flag_id>)*
<sip_decl> ::= "of" <sip_id> (<"," <sip_id>)*

<piece_id> ::= identifier <initializer>?
<chars_id> ::= identifier <initializer>?
<flag_id> ::= "of" identifier <flag_initializer>?
<sip_id> ::= identifier <sip_initializer>?

<initializer> ::= "=" <value>
<flag_initializer> ::= "=" ("up" | "down")
<sip_initializer> ::= "=" <sip_value>

<value> ::= integer_literal | string_literal | array_literal | table_literal
<sip_value> ::= float_literal
```

### Table Declaration

```ebnf
<table_decl> ::= "table" "of" identifier "=" "[" <table_field>* "]" ";"

<table_field> ::= <piece_table_field>
                | <chars_table_field>
                | <flag_table_field>
                | <sip_table_field>

<piece_table_field> ::= "piece" "of" identifier ";"
<chars_table_field> ::= "chars" <array_suffix>? "of" identifier ";"
<flag_table_field> ::= "flag" "of" identifier ";"
<sip_table_field> ::= "sip" "of" identifier ";"

<array_suffix> ::= "[]" | "[][]" | ...  // Multi-dimensional arrays
```

## Function/Recipe Declarations

```ebnf
<recipe_decl> ::= "prepare" <recipe_return_type> "of" identifier 
                  "(" <parameter_list>? ")" "{" <local_decl>* <statement>* "}"

<recipe_return_type> ::= "piece" | "chars" | "flag" | "sip" | identifier

<parameter_list> ::= <parameter> ("," <parameter>)*

<parameter> ::= ("piece" | "chars" | "flag" | "sip") <array_suffix>? "of" identifier

<local_decl> ::= ("piece" | "chars" | "flag" | "sip") <array_suffix>? 
                 "of" identifier <local_initializer>? ";"
                | "piece" "of" identifier ("," identifier)* ";"
                | "chars" <array_suffix>? "of" identifier ("," identifier)* ";"
                | "flag" "of" identifier ("," identifier)* ";"
                | "sip" "of" identifier ("," identifier)* ";"

<local_initializer> ::= "=" <expression>
```

## Expressions

### Primary Expressions

```ebnf
<primary_expr> ::= integer_literal
                 | string_literal
                 | float_literal
                 | "up" | "down"
                 | identifier
                 | array_literal
                 | table_literal
                 | "(" <expression> ")"
                 | <function_call>

<array_literal> ::= "[" (<expression> ("," <expression>)*)? "]"

<table_literal> ::= "[" (<field_init> (";" <field_init>)*)? "]"

<field_init> ::= identifier "=" <expression>

<function_call> ::= identifier "(" (<expression> ("," <expression>)*)? ")"
```

### Postfix Expressions

```ebnf
<postfix_expr> ::= <primary_expr>
                 | <postfix_expr> "[" <expression> "]"  // Array access
                 | <postfix_expr> "." identifier        // Field access

<call_expr> ::= <postfix_expr>
              | identifier "(" (<expression> ("," <expression>)*)? ")"
```

### Unary Expressions

```ebnf
<unary_expr> ::= <call_expr>
               | "-" <unary_expr>
               | "!" <unary_expr>
```

### Multiplicative Expressions

```ebnf
<mult_expr> ::= <unary_expr>
              | <mult_expr> "*" <unary_expr>
              | <mult_expr> "/" <unary_expr>
              | <mult_expr> "%" <unary_expr>
```

### Additive Expressions

```ebnf
<add_expr> ::= <mult_expr>
             | <add_expr> "+" <mult_expr>
             | <add_expr> "-" <mult_expr>
```

### Relational Expressions

```ebnf
<rel_expr> ::= <add_expr>
             | <rel_expr> "<" <add_expr>
             | <rel_expr> ">" <add_expr>
             | <rel_expr> "<=" <add_expr>
             | <rel_expr> ">=" <add_expr>
```

### Equality Expressions

```ebnf
<eq_expr> ::= <rel_expr>
            | <eq_expr> "==" <rel_expr>
            | <eq_expr> "!=" <rel_expr>
```

### Logical AND

```ebnf
<and_expr> ::= <eq_expr>
             | <and_expr> "&&" <eq_expr>
```

### Logical OR

```ebnf
<or_expr> ::= <and_expr>
            | <or_expr> "||" <and_expr>
```

### Complete Expression

```ebnf
<expression> ::= <or_expr>
```

## Statements

```ebnf
<statement> ::= <block>
              | <declaration_statement>
              | <expression_statement>
              | <if_statement>
              | <switch_statement>
              | <for_loop>
              | <do_while_loop>
              | <return_statement>
              | <break_statement>
              | <continue_statement>

<block> ::= "{" <statement>* "}"

<declaration_statement> ::= <piece_decl_stmt>
                          | <chars_decl_stmt>
                          | <flag_decl_stmt>
                          | <sip_decl_stmt>

<piece_decl_stmt> ::= "piece" <array_suffix>? "of" identifier 
                      ("=" <expression>)? 
                      ("," identifier ("=" <expression>)?)* ";"

<chars_decl_stmt> ::= "chars" <array_suffix>? "of" identifier 
                      ("=" <expression>)? 
                      ("," identifier ("=" <expression>)?)* ";"

<flag_decl_stmt> ::= "flag" "of" identifier 
                     ("=" ("up" | "down"))? 
                     ("," identifier ("=" ("up" | "down"))?)* ";"

<sip_decl_stmt> ::= "sip" "of" identifier 
                    ("=" <expression>)? 
                    ("," identifier ("=" <expression>)?)* ";"

<expression_statement> ::= <expression> ";"

<assignment_stmt> ::= <expression> <assignment_op> <expression> ";"

<assignment_op> ::= "=" | "+=" | "-=" | "*=" | "/=" | "%="
```

### Conditional Statements

```ebnf
<if_statement> ::= "check" "(" <expression> ")" <block>
                   (<elseif_part>)*
                   (<else_part>)?

<elseif_part> ::= "alt" "(" <expression> ")" <block>

<else_part> ::= "instead" <block>
```

### Switch Statements

```ebnf
<switch_statement> ::= "menu" "(" <expression> ")" "{" <case>* (<default_case>)? "}"

<case> ::= "choice" <case_value> ":" <statement>*

<case_value> ::= integer_literal | string_literal

<default_case> ::= "usual" ":" <statement>*
```

### Loop Statements

#### For Loop

```ebnf
<for_loop> ::= "pass" "(" <for_init> ";" <for_condition> ";" <for_update> ")" <block>

<for_init> ::= <declaration_statement> | <expression_statement>

<for_condition> ::= <expression>

<for_update> ::= <expression_statement>
```

#### Do-While Loop

```ebnf
<do_while_loop> ::= "order" <block> "repeat" "(" <expression> ")" ";"
```

### Jump Statements

```ebnf
<return_statement> ::= "serve" <expression>? ";"

<break_statement> ::= "stop" ";"

<continue_statement> ::= "next" ";"
```

## Terminals (Tokens)

### Reserved Words (Keywords)

```ebnf
<keyword> ::= "piece" | "chars" | "flag" | "sip" | "table" | "of"
            | "prepare" | "serve" | "start"
            | "check" | "alt" | "instead"
            | "menu" | "choice" | "usual"
            | "pass" | "order" | "repeat"
            | "stop" | "next"
            | "bill"
            | "up" | "down"
```

### Operators and Punctuation

```ebnf
<operator> ::= "+" | "-" | "*" | "/" | "%"
             | "<" | ">" | "<=" | ">=" | "==" | "!="
             | "&&" | "||" | "!"
             | "=" | "+=" | "-=" | "*=" | "/=" | "%="

<punctuation> ::= "(" | ")" | "{" | "}" | "[" | "]"
                | ";" | "," | "."
```

### Literals

```ebnf
<integer_literal> ::= [+-]?[0-9]+

<float_literal> ::= [+-]?[0-9]+\.[0-9]+

<string_literal> ::= "\"" ([^\"]|\\\")* "\""

<identifier> ::= [a-zA-Z_][a-zA-Z0-9_]*

<array_suffix> ::= "[]" | "[][]" | "[][][]" | ...
```

## Grammar Properties

### Left Recursion
The grammar avoids direct left recursion in expression rules through use of postfix operators.

### Ambiguity
The grammar is **unambiguous** with precedence rules and associativity defined above.

### Parser Type
The grammar is suitable for **LALR(1)** parsing, which allows efficient bottom-up parsing.

## Semantic Rules

Beyond the pure syntax, these semantic rules apply:

1. **Variable Scope**: Global variables accessible from all recipes
2. **Variable Shadowing**: Not allowed - local variables cannot have same name as globals
3. **Type Checking**: All operations must have compatible types
4. **Forward References**: Recipes can be called before declaration
5. **Function Uniqueness**: No two functions can have same name
6. **Start Block Requirement**: Every Platter program must have a `start()` block

## Example: Grammar Derivation

### Platter Code
```platter
prepare piece of add(piece of x, piece of y) {
    serve x + y;
}
```

### Grammar Derivation Path
```
<program>
  → <global_decl>* <recipe_decl>* <start_block>
  → <recipe_decl>
  → "prepare" <recipe_return_type> "of" identifier "(" <parameter_list> ")" "{" <statement>* "}"
  → "prepare" "piece" "of" "add" "(" <parameter> "," <parameter> ")" "{" <return_statement> "}"
  → "prepare" "piece" "of" "add" "(" 
      ("piece" "of" "x") "," ("piece" "of" "y")
    ")" "{" "serve" <expression> ";" "}"
  → "prepare" "piece" "of" "add" "(" 
      ("piece" "of" "x") "," ("piece" "of" "y")
    ")" "{" "serve" (x "+" y) ";" "}"
```

---

See also:
- [Lexical Analysis Reference](LEXER_REFERENCE.md)
- [AST & Parse Tree Guide](AST_GUIDE.md)
- [Platter Language User Guide](../README.md)
