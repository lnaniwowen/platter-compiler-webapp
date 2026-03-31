# Browser IDE Features

## Overview

The Platter Compiler Webapp is a modern, feature-rich integrated development environment (IDE) for the Platter programming language. It runs entirely in your web browser with zero server dependencies.

## ✨ Core Features

### 1. Code Editor

**Features:**
- Syntax highlighting for Platter language
- Line numbering
- Automatic indentation
- Code folding
- Multiple editor themes (dark/light)
- CodeMirror 5 integration

**Keyboard Shortcuts:**
- `Ctrl+/` - Toggle line comment
- `Ctrl+Z` - Undo
- `Ctrl+Y` - Redo
- `Ctrl+F` - Find
- `Tab` - Indent
- `Shift+Tab` - Unindent

### 2. File Operations

**Capabilities:**
- Create new Platter files
- Open `.platter` files from disk
- Save files locally
- Download compiled results
- File management interface

**Supported Formats:**
- `.platter` - Platter source files
- `.txt` - Text files for output

### 3. Compilation Pipeline

The IDE performs complete compilation with detailed analysis at each stage:

#### Phase 1: Lexical Analysis (Tokenization)
- Input: Raw source code
- Output: Token stream with classification
- Detects: Invalid characters, unclosed strings/comments

**Displayed Information:**
- Token type (keyword, identifier, operator, literal, etc.)
- Token value
- Line and column position
- Token classification

**Example:**
```
Input: piece of count = 10;

Output:
  keyword: piece
  keyword: of
  identifier: count
  operator: =
  number: 10
  punctuation: ;
```

#### Phase 2: Syntax Analysis (Parsing)
- Input: Token stream
- Output: Parse tree / Abstract Syntax Tree (AST)
- Validates: Grammar and syntax rules
- Detects: Syntax errors with recovery

**Status Indicators:**
- ✅ "No Syntax Error" - Code is syntactically valid
- ❌ "Syntax Error: ..." - Shows error location and message
- Suggests recovery points

**Error Information:**
- Error type (unexpected token, expected symbol, etc.)
- Location (line, column)
- Context (surrounding tokens)

#### Phase 3: Semantic Analysis
- Input: AST
- Output: Symbol table, type information
- Validates: Type compatibility, scope rules, flow control

**Analysis Includes:**
- Symbol Table: Variable declarations, types, scopes
- AST Structure: Visual or text representation
- Error Detection: Type mismatches, undefined symbols, etc.

**Error Categories:**
- Undefined variable references
- Duplicate declarations
- Type incompatibility
- Scope violations
- Control flow errors

### 4. Intermediate Code Generation

**Capability:**
- Generates Three-Address Code (TAC) representation
- Shows optimization analysis
- Displays instruction sequences

**TAC Output Details:**
- Instruction type (ASSIGN, BINOP, CALL, LABEL, etc.)
- Operands and results
- Function decomposition
- Basic block structure

**Optimization Information:**
- Constant folding results
- Dead code elimination
- Copy propagation effects
- Strength reduction opportunities

### 5. Real-Time Validation

**Features:**
- Errors update as you type
- Background analysis (non-blocking)
- Progressive error reporting
- Syntax highlighting updates

**Error Panel:**
- Clear error messages
- Line references
- Error severity levels
- Suggested fixes (when available)

### 6. Code Structure Visualization

**Display Options:**
- **AST View**: Abstract Syntax Tree representation
  - Expandable tree structure
  - Node type indication
  - Property details

- **Symbol Table**: All declared symbols with:
  - Variable names
  - Types
  - Scope information
  - Declaration location
  - Usage count
  - Initialization status

- **Control Flow**: Block and statement structure

## 🚀 Advanced Features

### 1. Theme Support

**Available Themes:**
- Light Theme: High contrast, easy on eyes in bright environments
- Dark Theme: Reduced eye strain in low-light environments

**Settings:** Accessible from IDE preferences

### 2. Performance Optimization

**Client-Side Execution:**
- All analysis runs in your browser
- No server roundtrips
- Instant feedback
- Works offline
- Zero cloud dependencies

**Backend Technology:**
- Python runtime via Pyodide (WebAssembly)
- Compiled Python bytecode for speed
- Efficient memory management

### 3. Multi-file Awareness

**Features:**
- Single-file analysis in current version
- Future: Multi-file project support
- Library/module organization

### 4. Configuration

**Editor Settings:**
- Tab size (default: 4 spaces)
- Auto-indent enabling
- Line wrap options
- Font size adjustment
- Color scheme selection

**Compiler Settings:**
- Optimization level selection
- Debug symbol generation
- Error reporting verbosity

## 📊 Output Panels

### 1. Lexical Analysis Panel
Displays all tokens with:
- Token type classification
- Token value
- Position (line:column)
- Token sequence summary

### 2. Syntax Analysis Panel
Shows:
- Parse status (valid/error)
- Error messages with context
- AST structure (when valid)

### 3. Semantic Analysis Panel
Displays:
- Type information
- Variable declarations
- Function signatures
- Error details

### 4. Intermediate Code Panel
Shows:
- TAC instructions
- Function decomposition
- Basic blocks
- Label definitions

### 5. Error Summary Panel
Lists all issues:
- Count by severity
- Navigable error list
- Quick jump to line

## 💡 Usage Tips

### 1. Getting Started
```platter
// Start with the main function
start() {
    bill("Hello, Platter!");
}
```

### 2. Debugging with Print Statements
```platter
piece of x = 5;
bill(x);  // Output value to console
```

### 3. Checking Intermediate Code
- After code is syntactically valid
- Review TAC for optimization opportunities
- Understand execution flow

### 4. Using the Symbol Table
- Verify variable declarations
- Check type assignments
- Understand scope relationships

## 🔧 Troubleshooting

### Issue: "Unclosed String" Error
**Cause:** Missing closing double quote
**Solution:** Check all string literals end with `"`

**Example:**
```platter
// Wrong
chars of msg = "Hello;

// Correct
chars of msg = "Hello";
```

### Issue: "Undefined Symbol" Error
**Cause:** Variable used before declaration
**Solution:** Declare variables before use

**Example:**
```platter
// Wrong
start() {
    bill(x);  // x not declared
}

// Correct
piece of x = 5;
start() {
    bill(x);
}
```

### Issue: "Type Mismatch" Error
**Cause:** Operation between incompatible types
**Solution:** Ensure type compatibility

**Example:**
```platter
// Wrong
piece of count = 5;
piece of sum = count + "text";  // Cannot add int and string

// Correct
chars of text = "Count is 5";
bill(text);
```

### Issue: "Duplicate Declaration" Error
**Cause:** Variable declared twice in same scope
**Solution:** Use unique variable names

**Example:**
```platter
// Wrong
piece of x = 5;
piece of x = 10;  // Error: x already declared

// Correct
piece of x = 5;
piece of y = 10;
```

### Browser Compatibility

**Supported Browsers:**
- Chrome/Chromium 60+
- Firefox 55+
- Safari 11+
- Edge 79+
- Opera 47+

**Requirements:**
- WebAssembly support
- Pyodide 0.29+
- 100MB+ available RAM

## 📱 Responsive Design

**Layouts:**
- **Desktop**: Full editor with side panels
- **Tablet**: Stacked panels with touch controls
- **Mobile**: Single column with expandable sections

## 🔐 Privacy

**Data Handling:**
- All code stays on your machine
- No data sent to servers
- No tracking or analytics
- Fully open source

## ⚡ Performance

**Analysis Speed:**
- Lexical analysis: < 100ms for typical files
- Syntax analysis: < 500ms for typical files
- Semantic analysis: < 1000ms for typical files
- TAC generation: < 500ms for typical files

**Memory Usage:**
- Typical file: 5-50MB (including Python runtime)
- Runtime + IDE overhead: ~300MB
- Most overhead in WebAssembly runtime

## 🛠️ Development

### Running Locally

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build
```

### Deployment

The IDE can be deployed to any static hosting:
- GitHub Pages
- Netlify
- Vercel
- AWS S3 + CloudFront
- Any web server

**Build Output:** `platter-compiler-sveltejs/build/` directory

## 📚 Related Resources

- [Platter Language User Guide](../README.md)
- [Compiler Architecture](../platter-compiler-sveltejs/README.md)
- [Grammar Specification](GRAMMAR.md)
- [Lexical Analysis Reference](LEXER_REFERENCE.md)
- [AST & Parse Tree Guide](AST_GUIDE.md)

## 🎯 Future Enhancements

Planned features:
- Multi-file project support
- Debug breakpoints and stepping
- Performance profiling
- Code formatting/pretty-print
- Find and replace globally
- Custom syntax themes
- Plugin system
- Collaborative editing
- Version history/undo stack

---

Last Updated: March 2026
