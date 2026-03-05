<expand-macro-skill>

<task>
Demystify Rust macros by breaking down their anatomy and showing step-by-step expansion.

**Supports:**
- `macro_rules!` declarative macros
- `#[derive(...)]` procedural macros
- Common attribute macros
</task>

<input>

```rust
$ARGUMENTS
```
</input>

<context_gathering>

**MANDATORY — do this BEFORE expanding the macro**

1. **Locate the code** — If $ARGUMENTS is a file:line or function name, use Read/Grep/Glob to find the actual code. If it's a code snippet, use it directly.

2. **Find the macro definition** — Grep for `macro_rules! name` or the derive macro's crate. Read the full definition including all match arms and transcribers.

3. **Find invocation sites** — Grep for `name!` or `#[derive(Name)]` across the codebase to see how the macro is actually used and with what arguments.

4. **Read the expanded context** — Read the surrounding code at invocation sites to understand what the expansion needs to fit into (type expectations, trait bounds).

5. **Check for helper macros** — If the macro calls other macros internally, read those definitions too for full expansion understanding.

Only proceed after locating the macro definition and understanding its invocation context.
</context_gathering>

<identification>
First, identify the macro type:

1. **If input contains `#[derive(...)]`** → Use DERIVE MACRO section
2. **If input contains `macro_rules!`** → Use DECLARATIVE MACRO section
3. **If input is a macro invocation** (e.g., `vec![]`, `println!()`) → Explain what it expands to
</identification>

<derive_macros>

### Mental Model: Trait Implementation Generator

`#[derive(Trait)]` is a **procedural macro** that:
1. Reads your struct/enum definition
2. Generates trait implementations automatically
3. Each derive is independent - order doesn't matter

### Standard Library Derives

| Derive | Trait | What You Get |
|--------|-------|--------------|
| `Clone` | `Clone` | `.clone()` method - deep copy |
| `Copy` | `Copy` | Implicit bitwise copy (requires `Clone`) |
| `Debug` | `Debug` | `{:?}` formatting for debugging |
| `Default` | `Default` | `Type::default()` constructor |
| `PartialEq` | `PartialEq` | `==` and `!=` operators |
| `Eq` | `Eq` | Marker: total equality (requires `PartialEq`) |
| `PartialOrd` | `PartialOrd` | `<`, `>`, `<=`, `>=` (requires `PartialEq`) |
| `Ord` | `Ord` | Total ordering (requires `PartialOrd` + `Eq`) |
| `Hash` | `Hash` | Can be `HashMap`/`HashSet` key (requires `Eq`) |

### Common External Derives

| Crate | Derive | Purpose |
|-------|--------|---------|
| serde | `Serialize`, `Deserialize` | JSON/YAML/etc serialization |
| thiserror | `Error` | Custom error types |
| clap | `Parser` | CLI argument parsing |
| sqlx | `FromRow` | Database row mapping |

### Derive Output Format

#### DERIVE EXPANSION

For input like:
```rust
#[derive(Clone, Debug, PartialEq)]
pub struct Name(Type);
```

Show:
1. **Original struct** (unchanged)
2. **Generated impl for each derive**
3. **What methods/traits are now available**

#### DERIVE REQUIREMENTS

| Derive | Requires | Field Requirements |
|--------|----------|-------------------|
| `Clone` | - | All fields: `Clone` |
| `Copy` | `Clone` | All fields: `Copy` |
| `Debug` | - | All fields: `Debug` |
| `PartialEq` | - | All fields: `PartialEq` |
| `Eq` | `PartialEq` | All fields: `Eq` |
| `Hash` | - | All fields: `Hash` |
| `Default` | - | All fields: `Default` |

#### COMMON DERIVE COMBINATIONS

| Use Case | Derives |
|----------|---------|
| Simple value type | `Clone, Debug` |
| HashMap key | `Clone, Debug, PartialEq, Eq, Hash` |
| Sortable | `Clone, Debug, PartialEq, Eq, PartialOrd, Ord` |
| Serializable | `Clone, Debug, Serialize, Deserialize` |
| Error type | `Debug, thiserror::Error` |

#### HOW TO SEE ACTUAL EXPANSION

```bash
cargo install cargo-expand
cargo expand --lib           # whole crate
cargo expand path::to::item  # specific item
```
</derive_macros>

<declarative_macros>

### Mental Model: Smart Find-and-Replace

`macro_rules!` is a compile-time pattern matcher that:
1. **Matches** patterns in your code (the "matcher")
2. **Captures** parts of the input into variables
3. **Generates** new code using those captured parts (the "transcriber")

It operates on **tokens**, not text - so it's type-aware and hygienic.

### Process

1. **Identify the Structure**:
   ```rust
   macro_rules! name {
       (MATCHER) => { TRANSCRIBER };  // rule 1
       (MATCHER) => { TRANSCRIBER };  // rule 2 (fallback)
   }
   ```

2. **Break Down the Matcher** - What pattern does it look for?

3. **Identify Fragment Types** - What kind of tokens are captured?

4. **Analyze Repetitions** - `$(...)*`, `$(...)+`, `$(...)?`

5. **Show Expansion** - Step through an example invocation

### Fragment Specifiers Reference

| Specifier | Matches | Example |
|-----------|---------|---------|
| `$x:ident` | Identifier | `foo`, `my_var` |
| `$x:expr` | Expression | `1 + 2`, `foo()` |
| `$x:ty` | Type | `i32`, `Vec<String>` |
| `$x:pat` | Pattern | `Some(x)`, `_` |
| `$x:stmt` | Statement | `let x = 1;` |
| `$x:block` | Block | `{ ... }` |
| `$x:item` | Item | `fn`, `struct`, `impl` |
| `$x:meta` | Attribute content | `derive(Debug)` |
| `$x:tt` | Token tree | Anything! |
| `$x:literal` | Literal | `"hello"`, `42` |
| `$x:path` | Path | `std::io::Result` |
| `$x:lifetime` | Lifetime | `'a`, `'static` |
| `$x:vis` | Visibility | `pub`, `pub(crate)`, (empty) |

### Repetition Operators

| Operator | Meaning | Matches |
|----------|---------|---------|
| `$(...)*` | Zero or more | `a, b, c` or empty |
| `$(...)+` | One or more | `a, b, c` (at least one) |
| `$(...)?` | Zero or one | `a` or empty |

Separators: `$($x:expr),*` - comma-separated, `$($x:expr);*` - semicolon-separated

### Output Format for macro_rules!

#### MACRO ANATOMY

```rust
macro_rules! NAME {
    // +--------------- MATCHER ---------------+
    // |                                       |
       ( $( $pattern:specifier ),* ) => {
    // +---------------------------------------+

    // +------------- TRANSCRIBER -------------+
    // |                                       |
           $( generated code using $pattern )*
    // +---------------------------------------+
       };
}
```

#### MATCHER BREAKDOWN

| Part | What It Captures | Type |
|------|------------------|------|
| `$name` | [explanation] | [fragment type] |

#### REPETITION ANALYSIS

- Repetition pattern: `$(...)*` / `$(...)+` / `$(...)?`
- Separator: [comma/semicolon/none]
- What repeats: [which captures]

#### EXPANSION EXAMPLE

Given invocation:
```rust
[example macro call]
```

**Step 1: Pattern Matching**
```
Macro sees: [tokens]
Captures:
  $var1 = [value]
  $var2 = [value]
```

**Step 2: Substitution**
```
Template:      [transcriber with $vars]
Substituted:   [concrete code]
```

**Step 3: Final Generated Code**
```rust
[what the compiler actually sees]
```
</declarative_macros>

<examples>

### Example: macro_rules!

Input macro:
```rust
macro_rules! vec_of {
    ( $( $x:expr ),* ) => {
        {
            let mut temp_vec = Vec::new();
            $( temp_vec.push($x); )*
            temp_vec
        }
    };
}
```

**ANATOMY:**
- Matcher: `$( $x:expr ),*` - zero or more expressions, comma-separated
- Transcriber: Creates vec, pushes each expression, returns vec

**EXPANSION of `vec_of![1, 2, 3]`:**

Step 1 - Capture:
```
$x matches: 1, 2, 3 (three times)
```

Step 2 - Expand repetition `$( ... )*`:
```rust
{
    let mut temp_vec = Vec::new();
    temp_vec.push(1);  // first $x
    temp_vec.push(2);  // second $x
    temp_vec.push(3);  // third $x
    temp_vec
}
```

---

### Example: Derive Macro

Input:
```rust
#[derive(Clone, Debug, PartialEq, Eq, Hash)]
pub struct Email(String);
```

**EXPANSION:**

```rust
pub struct Email(String);

impl Clone for Email {
    fn clone(&self) -> Self {
        Email(self.0.clone())
    }
}

impl std::fmt::Debug for Email {
    fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
        f.debug_tuple("Email").field(&self.0).finish()
    }
}

impl PartialEq for Email {
    fn eq(&self, other: &Self) -> bool {
        self.0 == other.0
    }
}

impl Eq for Email {}

impl std::hash::Hash for Email {
    fn hash<H: std::hash::Hasher>(&self, state: &mut H) {
        self.0.hash(state);
    }
}
```

**WHAT YOU CAN NOW DO:**
- `email.clone()` - duplicate the value
- `println!("{:?}", email)` - debug print
- `email1 == email2` - equality comparison
- `HashMap<Email, V>` - use as map key
</examples>

<rules>
- ALWAYS identify macro type first (derive vs macro_rules!)
- ALWAYS show concrete expansion example
- For macro_rules!: explain fragment types in plain English
- For derives: show the generated impl blocks
- Point out requirements and gotchas
- Suggest `cargo expand` for complex cases
</rules>

<success_criteria>
- Macro type (derive/declarative/attribute) is correctly identified
- Concrete expansion example is shown with step-by-step breakdown
- Fragment types explained in plain English for macro_rules!
- Generated impl blocks shown for derive macros
- Requirements and gotchas are called out
- `cargo expand` suggested for complex cases
</success_criteria>

</expand-macro-skill>
