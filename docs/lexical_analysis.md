# Lexical Analysis

## Learning Objectives

- Understand the role of the lexical analyzer in compilation
- Learn about tokens, patterns, and lexemes
- Explore lexical error handling
- Master regular expressions for token specification

---

## Guide Questions

1. **What is the role of the lexical analyzer?**
2. **Why should parsing be separated from lexical analysis?**
3. **What are tokens, patterns, and lexemes and what are the differences between these three?**
4. **What are lexical errors and how are they handled?**
5. **How is a lexical definition of a language specified?**

---

## The Role of the Lexical Analyzer

The lexical analyzer serves as the **first phase of the compiler** and performs several crucial functions:

- **Converts raw text into tokens** - Transforms the source code from a stream of characters into meaningful units
- **Strips out whitespace and comments** - Removes unnecessary formatting and documentation that doesn't affect program logic
- **Correlates error messages** - Links compiler errors back to specific locations in the source code
- **Tracks position information** - Maintains line numbers and column numbers for debugging purposes

### Example Flow

```
Source Code: "int x = 42; // this is a variable"
↓
Lexical Analyzer
↓
Tokens: [INT_KEYWORD, IDENTIFIER(x), ASSIGN_OP, NUMBER(42), SEMICOLON]
```

---

## Issues in Lexical Analysis

### Why Separate Parsing from Tokenization?

**1. Simplicity in Design**

- Each phase has a single, well-defined responsibility
- Lexical analyzer focuses only on character-level recognition
- Parser focuses only on grammatical structure

**2. Improvement in Efficiency**

- Specialized algorithms for each phase
- Better performance through focused optimization
- Reduced complexity in each component

**3. Compiler Portability Enhanced**

- Easy to adapt lexical analyzer for different character sets
- Grammar rules remain unchanged across platforms
- Modular design allows independent updates

---

## Tokens, Patterns, and Lexemes

### Definitions

- **Token** - A category or label for lexical units (like a part of speech in grammar)
- **Pattern** - The rule or regular expression that defines how to recognize a token
- **Lexeme** - The actual text from source code that matches a token's pattern

### Example Breakdown

| Component   | Value                     | Description          |
| ----------- | ------------------------- | -------------------- |
| **Token**   | `identifier`              | Category name        |
| **Pattern** | `/[A-Za-z_][A-Za-z0-9_]*` | Rule for recognition |
| **Lexeme**  | `"nCount"`                | Actual text found    |

### Fortran Statement Example

For the statement: `E = M * C ** 2`

| Lexeme | Token                                       | Attributes              |
| ------ | ------------------------------------------- | ----------------------- |
| `E`    | `<id, pointer to symbol-table entry for E>` | Variable identifier     |
| `=`    | `<assign_op>`                               | Assignment operator     |
| `M`    | `<id, pointer to symbol-table entry for M>` | Variable identifier     |
| `*`    | `<mult_op>`                                 | Multiplication operator |
| `C`    | `<id, pointer to symbol-table entry for C>` | Variable identifier     |
| `**`   | `<exp_op>`                                  | Exponentiation operator |
| `2`    | `<number, integer value 2>`                 | Numeric literal         |

### Important Notes

- **Tokens serve as terminal symbols** in a language's grammar
- **Reserved strings** are lexemes with specific meaning (keywords like `if`, `while`)
- **Attributes** provide additional information when multiple lexemes match the same token pattern

---

## Lexical Errors

### What Are Lexical Errors?

- Occur when **no pattern matches** the current sequence of characters
- Few errors are detectable at lexical level alone
- Example: `fi(a == f (x))` - `fi` might be a typo for `if`

### Error Recovery Strategies

**1. Panic Mode**

- Delete characters until a well-formed token can be created
- Simple but may skip over multiple errors

**2. Deletion**

- Remove one character from the problematic lexeme
- Try to match again with shortened string

**3. Insertion**

- Add a character to the current lexeme
- Attempt to create valid token

**4. Replacement**

- Replace one character in the current lexeme
- Most common for typos

**5. Transposition**

- Swap adjacent characters
- Handles common typing mistakes

---

## Specifications of Tokens

Tokens are specified using **regular expressions** - mathematical formulas that describe text patterns.

### Basic Concepts

**Alphabet (Σ)**

- Finite set of symbols
- Examples: ASCII (128 characters), Extended ASCII (256 characters)

**String**

- Finite sequence of symbols from an alphabet
- Each symbol must be from the defined alphabet Σ

**String Length**

- Number of symbols in string S
- Denoted by |S|
- Empty string (ε) has length 0

**Language**

- Set of strings defined over an alphabet
- If language L is over alphabet Σ, then L ⊆ Σ\*
- Σ\* = set of all possible strings from symbols in Σ

---

## String Set Operations

### Union (L₁ ∪ L₂)

- **Definition**: Set of strings in either L₁ or L₂ (or both)
- **Example**: If L₁ = {cat, dog} and L₂ = {bird, cat}, then L₁ ∪ L₂ = {cat, dog, bird}

### Concatenation (L₁L₂)

- **Definition**: Set of strings formed by concatenating each string in L₁ with each string in L₂
- **Example**: If L₁ = {a, b} and L₂ = {c, d}, then L₁L₂ = {ac, ad, bc, bd}

### Kleene Closure (L\*)

- **Definition**: Set containing ε and all strings formed by concatenating zero or more strings from L
- **Example**: If L = {a, b}, then L\* = {ε, a, b, aa, ab, ba, bb, aaa, aab, ...}

### Positive Closure (L⁺)

- **Definition**: Set of all strings formed by concatenating one or more strings from L
- **Example**: If L = {a, b}, then L⁺ = {a, b, aa, ab, ba, bb, aaa, aab, ...}
- **Note**: L⁺ = LL* (L concatenated with L*)

---

## Regular Expressions

Regular expressions are patterns that match strings in a language. They use special symbols to describe text patterns.

### Basic Symbols

| Symbol | Meaning               | Example                       |
| ------ | --------------------- | ----------------------------- |
| `a`    | Literal character 'a' | Matches exactly 'a'           |
| `ε`    | Empty string          | Matches nothing (zero length) |
| `∅`    | Empty set             | Matches no strings            |

### Operators

| Operator | Name | Description | Example |
|----------|------|-------------|---------|
| `|` | Union/OR | Either pattern | `a|b` matches 'a' or 'b' |
| `•` or concatenation | Concatenation | One after another | `ab` matches 'a' followed by 'b' |
| `*` | Kleene Star | Zero or more | `a*` matches '', 'a', 'aa', 'aaa'... |
| `+` | Plus | One or more | `a+` matches 'a', 'aa', 'aaa'... |
| `?` | Question | Zero or one | `a?` matches '' or 'a' |
| `[]` | Character class | Any character inside | `[abc]` matches 'a', 'b', or 'c' |
| `[^]` | Negated class | Any character NOT inside | `[^abc]` matches any char except 'a', 'b', 'c' |

### Common Patterns

| Pattern    | Regular Expression       | Description                                              |
| ---------- | ------------------------ | -------------------------------------------------------- |
| Identifier | `[a-zA-Z_][a-zA-Z0-9_]*` | Letter/underscore followed by letters/digits/underscores |
| Integer    | `[0-9]+`                 | One or more digits                                       |
| Float      | `[0-9]+\.[0-9]+`         | Digits, dot, digits                                      |
| Whitespace | `[ \t\n\r]+`             | One or more space characters                             |

### Examples in Action

```
Pattern: [0-9]+
Matches: "42", "123", "7"
Doesn't match: "abc", "12.5"

Pattern: [a-zA-Z][a-zA-Z0-9]*
Matches: "x", "variable1", "myVar"
Doesn't match: "123abc", "_private"
```

---

## Operator Precedence

When building regular expressions, operators have different precedence levels (like mathematical operations).

### Precedence Order (Highest to Lowest)

1. **Parentheses `()`** - Highest precedence

   - Used for grouping
   - Example: `(a|b)*` vs `a|b*`

2. **Closure operators `*`, `+`, `?`**

   - Apply to immediately preceding element
   - Example: `ab*` means `a` followed by zero or more `b`s

3. **Concatenation (implicit)**

   - Items next to each other are concatenated
   - Example: `abc` means `a` then `b` then `c`

4. **Union/OR `|`** - Lowest precedence
   - Separates alternative patterns
   - Example: `a|bc` means `a` OR `bc`

### Examples Showing Precedence

| Expression | Interpretation | Matches |
|------------|----------------|---------|
| `a|b*` | `a` OR (zero or more `b`s) | "a", "", "b", "bb", "bbb"... |
| `(a|b)*` | Zero or more of (`a` OR `b`) | "", "a", "b", "ab", "ba", "abb"... |
| `ab|c` | (`ab`) OR `c` | "ab", "c" |
| `a(b|c)` | `a` followed by (`b` OR `c`) | "ab", "ac" |

### Best Practice

- **Use parentheses liberally** to make intentions clear
- **When in doubt, add parentheses** to avoid confusion
- Example: `(a|b)*(c|d)+` is clearer than `a|b*c|d+`

---

## Quick Reference

### Token Recognition Process

1. **Read characters** from source code
2. **Apply patterns** using regular expressions
3. **Match longest possible** lexeme
4. **Generate token** with attributes
5. **Handle errors** if no pattern matches
6. **Repeat** until end of input

### Common Token Types

- **Keywords**: `if`, `while`, `class`, `public`
- **Identifiers**: Variable and function names
- **Literals**: Numbers, strings, booleans
- **Operators**: `+`, `-`, `*`, `/`, `==`, `!=`
- **Delimiters**: `(`, `)`, `{`, `}`, `;`, `,`
- **Whitespace**: Spaces, tabs, newlines (usually ignored)

---

## Guide Questions - Complete Answers

### 1. What is the role of the lexical analyzer?

The lexical analyzer serves as the **first phase of the compiler** with four main roles:

- **Tokenization**: Converts raw source code text into meaningful tokens
- **Cleaning**: Strips out whitespace, comments, and other non-essential characters
- **Position tracking**: Maintains line and column numbers for error reporting
- **Error correlation**: Links compiler messages back to specific source code locations

### 2. Why should parsing be separated from lexical analysis?

Separation provides three key benefits:

- **Simplicity in design**: Each phase has a single, focused responsibility - lexical analysis handles character recognition, parsing handles grammar structure
- **Improved efficiency**: Specialized algorithms can be optimized for each specific task
- **Enhanced portability**: The lexical analyzer can be easily adapted for different character sets while grammar rules remain unchanged across platforms

### 3. What are tokens, patterns, and lexemes and what are the differences between these three?

| Concept     | Definition                                                               | Example                            |
| ----------- | ------------------------------------------------------------------------ | ---------------------------------- |
| **Token**   | A category or label for lexical units (like grammatical parts of speech) | `identifier`, `number`, `operator` |
| **Pattern** | The rule or regular expression defining how to recognize a token         | `/[A-Za-z_][A-Za-z0-9_]*/`         |
| **Lexeme**  | The actual text from source code that matches a token's pattern          | `"count"`, `"42"`, `"+"`           |

**Key difference**: Tokens are categories, patterns are recognition rules, lexemes are actual text instances.

### 4. What are lexical errors and how

are they handled?

**Lexical errors** occur when no defined pattern matches the current sequence of characters in the source code.

**Five handling strategies**:

1. **Panic mode**: Delete characters until a valid token can be formed
2. **Deletion**: Remove one character from the problematic lexeme
3. **Insertion**: Add a character to create a valid token
4. **Replacement**: Replace one character (common for typos)
5. **Transposition**: Swap adjacent characters (handles typing mistakes)

### 5. How is a lexical definition of a language specified?

Lexical definitions use **regular expressions** built on these foundations:

- **Alphabet (Σ)**: Finite set of symbols (e.g., ASCII characters)
- **Strings**: Finite sequences of symbols from the alphabet
- **Languages**: Sets of strings defined over an alphabet
- **Regular expressions**: Mathematical patterns using operators like `*` (zero or more), `+` (one or more), `|` (union), and `[]` (character classes)

**Example specification**:

```
identifier: [a-zA-Z_][a-zA-Z0-9_]*
number: [0-9]+
whitespace: [ \t\n\r]+
```

---

_Remember: The lexical analyzer is your first line of defense in compilation - it transforms messy text into clean, categorized tokens that the parser can work with!_
