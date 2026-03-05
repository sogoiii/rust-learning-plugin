<pattern-match-skill>

<task>
Explain pattern matching in depth - what patterns match, how binding works, and advanced features.
</task>

<input>
```rust
$ARGUMENTS
```
</input>

<mental_model>
## Patterns as Templates

A pattern is a template that Rust tries to match against a value:
- **Literal patterns**: Match exact values (`1`, `"hello"`)
- **Variable patterns**: Match anything and bind to name (`x`)
- **Wildcard**: Match anything, ignore (`_`)
- **Destructuring**: Match structure and extract parts (`Some(x)`, `Point { x, y }`)
</mental_model>

<context_gathering>

**MANDATORY — do this BEFORE analyzing patterns**

1. **Locate the code** — If $ARGUMENTS is a file:line or function name, use Read/Grep/Glob to find the actual code. If it's a code snippet, use it directly.

2. **Find the type being matched** — Read or Grep for the enum/struct definition being matched on. List all variants/fields to assess exhaustiveness.

3. **Check for nested types** — If patterns destructure nested enums or structs, read those definitions too.

4. **Read surrounding match usage** — Grep for other `match` expressions on the same type in the codebase to see how others handle it.

5. **Check binding modes** — Note whether the match is on an owned value, `&T`, or `&mut T`, as this affects binding modes and match ergonomics.

Only proceed after understanding the full type definition and how the codebase matches on it.
</context_gathering>

<process>
1. **Identify Pattern Type** - What kind of pattern is this?

2. **Check Exhaustiveness** - Does it cover all cases?

3. **Analyze Bindings** - How are values captured?

4. **Explain Guards** - Any `if` conditions?

5. **Show Equivalent Code** - What does this pattern expand to?
</process>

<pattern_types_reference>
### Literal Patterns
```rust
match x {
    1 => "one",
    2 => "two",
    _ => "other",
}
```

### Variable Binding
```rust
match x {
    n => println!("{}", n),  // n binds to x's value
}
```

### Wildcard
```rust
match x {
    _ => println!("ignored"),  // Matches anything, no binding
}
```

### Or Patterns
```rust
match x {
    1 | 2 | 3 => "small",
    _ => "large",
}
```

### Range Patterns
```rust
match x {
    1..=5 => "one through five",
    _ => "other",
}
```

### Tuple Destructuring
```rust
match point {
    (0, 0) => "origin",
    (x, 0) => format!("on x-axis at {}", x),
    (0, y) => format!("on y-axis at {}", y),
    (x, y) => format!("at ({}, {})", x, y),
}
```

### Struct Destructuring
```rust
match point {
    Point { x: 0, y: 0 } => "origin",
    Point { x, y: 0 } => format!("on x-axis at {}", x),
    Point { x: 0, y } => format!("on y-axis at {}", y),
    Point { x, y } => format!("at ({}, {})", x, y),
}
```

### Enum Destructuring
```rust
match option {
    Some(x) => format!("got {}", x),
    None => "nothing".to_string(),
}
```

### Nested Patterns
```rust
match value {
    Some(Some(x)) => format!("deeply nested: {}", x),
    Some(None) => "outer Some, inner None".to_string(),
    None => "outer None".to_string(),
}
```
</pattern_types_reference>

<output_format>
### PATTERN ANALYSIS

```rust
[Annotated pattern with explanations]
```

### EXHAUSTIVENESS CHECK

| Pattern | Covers |
|---------|--------|
| `Pattern1` | [What values match] |
| `Pattern2` | [What values match] |
| `_` | [Everything else] |

**Exhaustive?** [Yes/No - if No, what's missing?]

### BINDING ANALYSIS

| Variable | What It Binds | Mode |
|----------|---------------|------|
| `x` | [description] | [move/ref/ref mut] |

### BINDING MODES EXPLAINED

**Default (Move):**
```rust
match opt {
    Some(x) => { /* x: T, moved out */ }
    //    ^ x takes ownership
}
```

**Reference (`ref`):**
```rust
match opt {
    Some(ref x) => { /* x: &T, borrowed */ }
    //   ^^^ x is a reference
}
```

**Mutable Reference (`ref mut`):**
```rust
match opt {
    Some(ref mut x) => { /* x: &mut T */ }
    //   ^^^^^^^ x is a mutable reference
}
```

**Match Ergonomics (auto-ref):**
```rust
match &opt {
    Some(x) => { /* x: &T, automatically borrowed */ }
    // When matching on &T, bindings become references
}
```

### GUARDS ANALYSIS

If the match expression has guards, include this analysis:

```rust
match x {
    n if n > 0 => "positive",
    //  ^^^^^^ guard - additional condition
    n if n < 0 => "negative",
    _ => "zero",
}
```

**Guard evaluation order:**
1. Pattern must match first
2. Then guard is evaluated
3. If guard false, try next arm

**Gotcha:** Guards don't contribute to exhaustiveness checking!

### @ BINDINGS

```rust
match x {
    n @ 1..=5 => println!("{} is small", n),
    // ^ bind the matched value to n
    n @ 6..=10 => println!("{} is medium", n),
    n => println!("{} is large", n),
}
```

Use `@` when you need both:
- The whole value
- To test against a pattern

### IRREFUTABLE vs REFUTABLE

**Irrefutable** (always matches):
```rust
let (x, y) = (1, 2);      // Always works for 2-tuple
let Point { x, y } = p;   // Always works for Point
```

**Refutable** (might not match):
```rust
if let Some(x) = opt { }  // Only matches Some
while let Some(x) = iter.next() { }
```

### EQUIVALENT CODE

Your pattern:
```rust
[original pattern]
```

Expands to approximately:
```rust
[equivalent if/else or explicit checks]
```
</output_format>

<advanced_patterns>
### Slice Patterns
```rust
match slice {
    [] => "empty",
    [x] => format!("one: {}", x),
    [x, y] => format!("two: {}, {}", x, y),
    [first, .., last] => format!("first: {}, last: {}", first, last),
    [first, rest @ ..] => format!("first: {}, rest: {:?}", first, rest),
}
```

### Nested Struct/Enum
```rust
match msg {
    Message::Move { x, y: 0 } => format!("move horizontal to {}", x),
    Message::Move { x: 0, y } => format!("move vertical to {}", y),
    Message::Write(text) if text.is_empty() => "empty write",
    Message::Write(text) => format!("write: {}", text),
    _ => "other",
}
```
</advanced_patterns>

<rules>
- ALWAYS check exhaustiveness
- Explain binding modes (move vs ref)
- Show match ergonomics when matching on references
- Warn about guards not affecting exhaustiveness
- Demonstrate equivalent desugared code for clarity
</rules>

<success_criteria>
- Every pattern arm in the input is identified and explained
- Exhaustiveness is explicitly checked and reported
- Binding modes (move/ref/ref mut/auto-ref) are correctly identified
- Guards are flagged with the exhaustiveness caveat
- Equivalent desugared code is provided for non-trivial patterns
</success_criteria>

</pattern-match-skill>
