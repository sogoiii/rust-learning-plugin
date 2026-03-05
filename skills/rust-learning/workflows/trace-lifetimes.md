<required_reading>
- references/borrow-ownership-rules.md
</required_reading>

<trace-lifetimes-skill>

<task>
Visualize and explain lifetime annotations so the user understands WHAT they mean, WHY they're needed, and HOW they constrain the code.
</task>

<input>

```rust
$ARGUMENTS
```

</input>

<mental_model>

## The Lease Analogy

Lifetimes are like lease agreements:
- **Data** = The property (apartment)
- **Reference** = The tenant
- **Lifetime `'a`** = The lease duration
- The compiler ensures no tenant stays after the property is demolished

</mental_model>

<context_gathering>

**MANDATORY — do this BEFORE analyzing lifetimes**

1. **Locate the code** — If $ARGUMENTS is a file:line or function name, use Read/Grep/Glob to find the actual code. If it's a code snippet, use it directly.

2. **Find the full definition** — If the signature is on a struct or trait, read the full definition including all fields and methods with lifetime annotations.

3. **Find all lifetime-annotated usages** — Grep for the struct/function name across the codebase to see how callers interact with the lifetime constraints.

4. **Read related types** — If lifetimes connect to other structs or traits, read their definitions to understand the full lifetime dependency chain.

5. **Check for elision candidates** — Count input references and check if the signature follows elision rules or needs explicit annotation.

Only proceed after understanding the full lifetime contract and how it's used in practice.
</context_gathering>

<process>

1. **Identify Lifetime Parameters** - List all `'a`, `'b`, `'static`, etc.

2. **Annotation Breakdown** - For each lifetime in the signature:
   ```
   fn get_longest<'a>(x: &'a str, y: &'a str) -> &'a str
                  ^^     ^^          ^^           ^^
                  │      │           │            │
                  │      │           │            └── Return valid for 'a
                  │      │           └── y lives at least 'a
                  │      └── x lives at least 'a
                  └── Define lifetime parameter 'a
   ```

3. **The Contract** - Explain what promises are being made:
   > By using `'a` for both inputs and output, you're saying: "The returned reference is borrowed from one of the inputs, so it cannot outlive EITHER of them."

4. **Scope Diagram** - Show how lifetimes interact with scopes:
   ```
   fn main() {
       let string1 = String::from("long");  //─────┐ 's1
       let result;                          //     │
       {                                    //     │
           let string2 = String::from("x"); //──┐  │ 's2
           result = longest(&string1, &string2);│  │
       } // string2 dropped ─────────────────────┘  │
       // result INVALID - tied to shorter lifetime │
       println!("{}", result); // ERROR!       │
   } // string1 dropped ────────────────────────────┘
   ```

5. **Elision Check** - Could this be simplified?
   - Single input reference → output gets same lifetime (elided)
   - `&self` method → output gets `self`'s lifetime (elided)
   - Multiple inputs → must annotate explicitly

6. **Common Patterns** - Identify which pattern this matches:
   - **Same lifetime for all** (`'a` everywhere): Output tied to shortest input
   - **Different lifetimes** (`'a`, `'b`): Inputs independent, output tied to one
   - **`'static`**: Data lives for entire program (string literals, leaked memory)
   - **Struct lifetimes**: Struct can't outlive its borrowed fields

</process>

<output_format>

### WHAT THE LIFETIMES MEAN
```
[Annotated signature with arrows/explanation]
```

### THE CONTRACT
> [Plain English explanation of what promises are made]

### SCOPE VISUALIZATION
```
[ASCII diagram showing lifetime spans in calling code]
```

### ELISION RULES CHECK
- [ ] Could be elided: [Yes/No]
- [ ] Why explicit: [Reason]

### COMMON GOTCHAS FOR THIS PATTERN
- [Gotcha 1]
- [Gotcha 2]

</output_format>

<patterns_reference>

### Pattern 1: Tied Lifetimes
```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str
```
Output tied to SHORTER of x, y. Use when returning either input.

### Pattern 2: Independent Lifetimes
```rust
fn first<'a, 'b>(x: &'a str, y: &'b str) -> &'a str
```
Output tied only to x. y's lifetime doesn't matter for return.

### Pattern 3: Struct Lifetimes
```rust
struct Excerpt<'a> {
    part: &'a str,
}
```
Struct cannot outlive the data it borrows.

### Pattern 4: Static Lifetime
```rust
fn get_static() -> &'static str {
    "I live forever"
}
```
String literals have `'static`. Be careful with `'static` bounds.

</patterns_reference>

<rules>

- ALWAYS draw scope diagrams - visualization is key
- Explain in terms of "outlives" relationships
- Show what would happen WITHOUT the lifetime (dangling reference)
- If code compiles, show a scenario where wrong lifetimes would fail

</rules>

<success_criteria>
- All lifetime parameters are identified and explained
- Annotation breakdown shows what each lifetime constrains
- The contract is explained in plain English
- Scope diagram visualizes lifetime spans with ASCII art
- Elision rules are checked and explained
- The matching lifetime pattern is identified
</success_criteria>

</trace-lifetimes-skill>
