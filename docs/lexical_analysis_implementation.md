# Lexical Analysis Implementation

## Overview

**Lexical Analysis** is the first phase of a compiler that breaks down source code into meaningful tokens. Think of it as reading a sentence and identifying each word, punctuation mark, and symbol.

### Key Concepts:
- **Token**: A meaningful unit (like keywords, identifiers, operators)
- **Lexeme**: The actual string that forms a token (e.g., "if", "x", "+")
- **Pattern**: Rules that describe how tokens are formed

---

## Recognition of Tokens

### Basic Assumptions
1. **Reserved words cannot be used as identifiers**
    - Example: You can't name a variable "if" or "while"
2. **Whitespace is ignored**
    - Spaces, tabs, and newlines don't affect token recognition

### Token Recognition Process
1. **Order token patterns by precedence**
       - Keywords before identifiers
       - Longer operators before shorter ones (">=" before ">")

2. **Use two pointers:**
       - **Lexeme-beginning pointer**: Marks start of current token
       - **Forward pointer**: Scans ahead through input

3. **Recognition steps:**
```
Input: "if (x >= 10)"

Step 1: Scan "if" → Recognized as KEYWORD
Step 2: Skip whitespace
Step 3: Scan "(" → Recognized as LEFT_PAREN
Step 4: Skip whitespace
Step 5: Scan "x" → Recognized as IDENTIFIER
Step 6: Skip whitespace
Step 7: Scan ">=" → Recognized as GTE_OPERATOR
Step 8: Skip whitespace
Step 9: Scan "10" → Recognized as NUMBER
Step 10: Scan ")" → Recognized as RIGHT_PAREN
```

---

## Transition Diagrams

A **transition diagram** is a visual representation of how a lexical analyzer recognizes tokens.

### Elements of a Transition Diagram:
1. **States** (circles): Represent current position in recognition
2. **Edges/Transitions** (arrows): Show movement between states
3. **Symbols** (labels on arrows): Characters that trigger transitions
4. **Start state**: Where recognition begins
5. **Accepting states**: Where valid tokens are recognized
6. **Determinism**: Each state has at most one transition for each input symbol

### Example: Recognizing Identifiers
```
Input: Letters followed by letters/digits

    letter
[Start] ──────→ [State1] ──────→ [Accept]
                   ↑        letter/digit
                   └─────────────────┘
```

### Example: Recognizing Numbers
```
Input: Digits, possibly with decimal point

    digit        digit
[Start] ──────→ [State1] ──────→ [Accept]
                   │
                   │ '.'
                   ↓        digit
                 [State2] ──────→ [State3] → [Accept]
                            ↑
                            └──── digit
```

---

## Implementation Details

### Core Functions:
- **`nextchar()`**: Reads next character, advances forward pointer
- **State management**: Each state has its own code segment
- **Backtracking**: When recognition fails, return to beginning

### Implementation Algorithm:
```pseudocode
function getNextToken():
    lexeme_begin = forward_pointer
    
    while (not end_of_input):
        current_char = nextchar()
        
        if (valid_transition_exists):
            move_to_next_state()
        else:
            if (current_state_is_accepting):
                retract_one_character()
                return create_token(current_lexeme)
            else:
                reset_to_beginning()
                try_next_pattern()
    
    return END_OF_FILE_TOKEN
```

### Symbol Table Integration:
- **Identifiers** must be stored in symbol table during lexical analysis
- **Keywords** can be stored in a hash table for quick lookup
- This reduces the number of states needed in transition diagrams

---

## Finite State Automata (FSA)

An **FSA** is a mathematical model for recognizing patterns in input.

### Components:
1. **States**: Finite set of conditions
2. **Alphabet**: Set of input symbols
3. **Transition function**: Maps (state, symbol) → state
4. **Start state**: Initial state
5. **Accepting states**: States that recognize valid input

### Example: Binary strings ending with "01"
```
Input alphabet: {0, 1}

       0        0
[q0] ────→ [q1] ────→ [q1]
 │          │
 │1         │1
 ↓          ↓
[q0] ←──── [q2] ──── ((q3))
      0         1
```
- q0: Start state
- q3: Accepting state (double circle)
- Accepts: "01", "001", "101", "1001", etc.

---

## Guide Questions Answered

### 1. How are tokens recognized by a lexical analyzer?

Tokens are recognized through a **pattern matching process** using:

- **Transition diagrams** or **finite state automata** that define valid token patterns
- **Two-pointer technique** (lexeme-beginning and forward pointers) to scan input
- **Priority-based matching** where patterns are tried in order of precedence
- **Backtracking** when invalid patterns are encountered

**Example Process:**
```
Input: "count = 42"

1. Scan "count" → matches identifier pattern → TOKEN: ID("count")
2. Skip whitespace
3. Scan "=" → matches assignment operator → TOKEN: ASSIGN
4. Skip whitespace  
5. Scan "42" → matches number pattern → TOKEN: NUM(42)
```

### 2. What are the elements of a transition diagram?

The key elements are:

1. **States** (nodes/circles): Represent current position in pattern recognition
2. **Edges/Transitions** (arrows): Show valid moves between states based on input
3. **Symbols** (edge labels): Characters or character classes that trigger transitions
4. **Start state**: Initial state where recognition begins
5. **Accepting states**: Final states that indicate successful token recognition
6. **Determinism**: Property ensuring unique transitions (no ambiguity)

**Visual Example:**
```
Recognizing floating-point numbers:

    digit     digit        '.'       digit
[Start] → [Integer] → [DecPoint] → [Float]
           ↑                        ↑
           └── digit ────────────────┘
```

### 3. How are lexical analyzers implemented?

Lexical analyzers are implemented using:

**A. State-driven approach:**

- Each state corresponds to a code segment
- Transitions change program state
- `nextchar()` function advances through input

**B. Table-driven approach:**

- Transition table maps (current_state, input_symbol) → next_state
- Generic driver reads table and processes input

**C. Key implementation strategies:**

- **Error handling**: Backtrack when invalid patterns are found
- **Symbol table integration**: Store identifiers as they're recognized  
- **Keyword handling**: Pre-populate reserved words to distinguish from identifiers
- **Buffer management**: Efficiently handle large input files

**Sample Implementation Structure:**
```pseudocode
class LexicalAnalyzer:
    input_buffer
    forward_pointer
    lexeme_begin
    symbol_table
    keyword_table
    
    function getToken():
        skip_whitespace()
        lexeme_begin = forward_pointer
        
        # Try each token pattern in priority order
        if (try_keyword_pattern()):
            return keyword_token
        elif (try_identifier_pattern()):
            return identifier_token
        elif (try_number_pattern()):
            return number_token
        # ... other patterns
        else:
            return error_token
```

---

## FSA Activities Solutions

### Activity 1: FSA that accepts strings ending with "01"

```
States: {q0, q1, q2, q3}
Start state: q0
Accepting state: q3
Alphabet: {0, 1}

Transitions:
       0        1
q0 → q1,    q0 → q0
q1 → q1,    q1 → q2  
q2 → q1,    q2 → q0
q3 → q1,    q3 → q0 (q3 is accepting)

Diagram:
       0        0
[q0] ────→ [q1] ────→ [q1]
 │ ↑        │
 │ │0       │1
 │1└──── [q2] ──────→ ((q3))
 ↓           1
[q0]

Actually, let me correct this:

       0        0
[q0] ────→ [q1] ────→ [q1]
 │          │
 │1         │1
 ↓          ↓
[q0] ←──── [q2] ──── ((q3))
      0         1
```

**Test cases:**
- "01" ✓ (accepts)
- "001" ✓ (accepts) 
- "101" ✓ (accepts)
- "10" ✗ (rejects)
- "11" ✗ (rejects)

### Activity 2: FSA that accepts strings with an even number of a's

```
States: {q0, q1}
Start state: q0 (also accepting - 0 is even)
Accepting state: q0
Alphabet: {a, b}

Transitions:
q0 → q1 (on 'a'), q0 → q0 (on 'b')
q1 → q0 (on 'a'), q1 → q1 (on 'b')

Diagram:
        a
((q0)) ←→ [q1]
  ↑         ↑
  │b        │b  
  └─────────┘
```

**Test cases:**
- "" ✓ (0 a's - even)
- "aa" ✓ (2 a's - even)
- "bab" ✗ (1 a - odd)
- "baab" ✓ (2 a's - even)

### Activity 3: FSA that accepts strings with an odd number of b's

```
States: {q0, q1}
Start state: q0  
Accepting state: q1
Alphabet: {a, b}

Transitions:
q0 → q0 (on 'a'), q0 → q1 (on 'b')
q1 → q1 (on 'a'), q1 → q0 (on 'b')

Diagram:
         b
[q0] ←→ ((q1))
 ↑         ↑
 │a        │a
 └─────────┘
```

**Test cases:**
- "b" ✓ (1 b - odd)
- "bb" ✗ (2 b's - even) 
- "aba" ✓ (1 b - odd)
- "abab" ✗ (2 b's - even)

### Activity 4: FSA that accepts strings containing "ab" as a substring

```
States: {q0, q1, q2}
Start state: q0
Accepting state: q2
Alphabet: {a, b}

Transitions:
q0 → q1 (on 'a'), q0 → q0 (on 'b')
q1 → q1 (on 'a'), q1 → q2 (on 'b')  
q2 → q2 (on 'a'), q2 → q2 (on 'b')

Diagram:
       a        b
[q0] ────→ [q1] ────→ ((q2))
 │ ↑                    ↑
 │b│a                   │a,b
 ↓ └────────────────────┘
[q0]
```

**Test cases:**
- "ab" ✓ (contains "ab")
- "aab" ✓ (contains "ab") 
- "abbb" ✓ (contains "ab")
- "ba" ✗ (doesn't contain "ab")
- "a" ✗ (doesn't contain "ab")

---

## Summary

Lexical analysis is the foundation of compilation, transforming raw source code into meaningful tokens through:

- **Pattern matching** using FSAs and transition diagrams
- **Systematic scanning** with pointer-based techniques  
- **Priority-based recognition** to handle conflicts
- **Integration with symbol tables** for identifier management
