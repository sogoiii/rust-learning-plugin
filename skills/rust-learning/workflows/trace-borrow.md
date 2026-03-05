<required_reading>
- references/borrow-ownership-rules.md
</required_reading>

<trace-borrow-skill>

<task>
Proactively analyze borrowing patterns in the code. Show WHEN borrows start and end, WHERE conflicts could occur, and HOW to restructure if needed.
</task>

<input>

```rust
$ARGUMENTS
```

</input>

<mental_model>

## The Library Card System

Think of borrows like library cards:
- **&T (immutable borrow)** = Reading card - many people can read same book
- **&mut T (mutable borrow)** = Editing card - only one person can edit at a time
- **Conflict** = Someone tries to edit while others are reading

</mental_model>

<context_gathering>

**MANDATORY — do this BEFORE analyzing borrows**

1. **Locate the code** — If $ARGUMENTS is a file:line or function name, use Read/Grep/Glob to find the actual code. If it's a code snippet, use it directly.

2. **Read surrounding context** — Read the full function and its callers to understand the borrow scope boundaries.

3. **Find other borrows of the same variable** — Grep within the function/module for other references to the same variable. Identify all `&`, `&mut`, moves, and drops.

4. **Check related patterns** — Grep the codebase for similar borrowing patterns (e.g., same struct fields being borrowed, similar iterator usage).

5. **Identify lifetimes in scope** — Note any explicit lifetime annotations or NLL-relevant scope boundaries that affect borrow durations.

Only proceed after locating and understanding the real code in context.
</context_gathering>

<process>

1. **Identify All Variables** - List each variable and its mutability

2. **Track Borrow Events** - For each line, note:
   - Variable created (owner)
   - Reference taken (&T or &mut T)
   - Reference used
   - Reference dropped (last use or scope end)

3. **Build Timeline Diagram**:
   ```
   LINE │ EVENT                    │ ACTIVE BORROWS
   ─────┼──────────────────────────┼─────────────────
     1  │ let mut data = vec![];   │ data: OWNER
     2  │ let r1 = &data;          │ data: OWNER, r1: &data
     3  │ let r2 = &data;          │ data: OWNER, r1: &data, r2: &data
     4  │ println!("{}", r1);      │ data: OWNER, r1: &data (last use), r2: &data
     5  │ data.push(1);            │ CONFLICT! r2 still active
     6  │ println!("{}", r2);      │ data: OWNER, r2: &data (last use)
   ```

4. **Identify Conflict Zones** - Mark where rules are violated:
   ```
   ┌─── r1: &data ───────────────────┐
   │ ┌─── r2: &data ─────────────────┼───────┐
   │ │                               │       │
   ──┴─┴───────────────────────────────────────────
     2   3   4        5 (CONFLICT)   6
                      │
                      └── data.push() needs &mut
   ```

5. **Apply Rust's Rules**:
   - **Rule 1**: One &mut OR any number of & (not both)
   - **Rule 2**: References must be valid (no dangling)
   - **NLL**: Borrow ends at LAST USE, not scope end

6. **Suggest Restructuring** (if conflicts found):

   | Issue | Fix Strategy | Example |
   |-------|--------------|---------|
   | &mut during & | Reorder operations | Move mutation before reads |
   | Overlapping &mut | Use scopes | `{ let r = &mut x; } let r2 = &mut x;` |
   | Long-lived borrow | Clone or extract | `let copy = x.clone();` |

</process>

<output_format>

### BORROW TIMELINE
```
LINE │ CODE                      │ BORROWS ACTIVE
─────┼───────────────────────────┼────────────────
[timeline with each line and active borrows]
```

### CONFLICT ANALYSIS

**Conflicts Found:** [None / List of conflicts]

For each conflict:
```
CONFLICT at LINE X:
  - Active borrow: [reference] created at LINE Y
  - Conflicting action: [what's trying to happen]
  - Rule violated: [which aliasing rule]
```

### BORROW SPAN DIAGRAM
```
[ASCII art showing overlapping borrow spans]
```

### RESTRUCTURING SUGGESTIONS (if needed)
1. **[Strategy Name]**: [Code example]
2. **[Strategy Name]**: [Code example]

### NLL NOTE
> [Explain if Non-Lexical Lifetimes affect this code - borrows ending early]

</output_format>

<patterns_reference>

### Safe: Sequential Mutations
```rust
let mut v = vec![1];
v.push(2);  // &mut ends
v.push(3);  // new &mut, OK
```

### Safe: Non-overlapping Borrows (NLL)
```rust
let mut v = vec![1];
let r = &v[0];
println!("{}", r);  // r's LAST USE - borrow ends here
v.push(2);          // OK! r is no longer active
```

### Unsafe: Overlapping Mutable
```rust
let mut v = vec![1];
let r = &mut v;
v.push(2);  // ERROR: v already borrowed as &mut
*r = vec![3];
```

### Unsafe: Mixed Mutability
```rust
let mut v = vec![1];
let r = &v;      // immutable borrow
v.push(2);       // ERROR: needs &mut while & exists
println!("{}", r);
```

</patterns_reference>

<rules>

- ALWAYS build the timeline first - it reveals patterns
- Note where NLL helps (borrows ending early)
- Even if code compiles, show the borrow structure
- Suggest IDIOMATIC fixes, not just any fix that works

</rules>

<success_criteria>
- Timeline diagram is complete with all borrow events
- All conflicts are identified and explained with rule references
- Borrow span diagram visually shows overlapping borrows
- Restructuring suggestions are idiomatic Rust
- NLL effects are noted where applicable
</success_criteria>

</trace-borrow-skill>
