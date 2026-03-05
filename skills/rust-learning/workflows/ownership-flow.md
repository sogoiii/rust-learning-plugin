<required_reading>
- references/borrow-ownership-rules.md
</required_reading>

<ownership-flow-skill>

<task>
Visualize how ownership flows through the code - showing moves, borrows, clones, and drops.
</task>

<input>

```rust
$ARGUMENTS
```

</input>

<mental_model>

## The Box Metaphor

- **Owned value** = You're holding a box
- **Move** = Hand the box to someone else (you no longer have it)
- **Borrow (`&`)** = Let someone look inside your box (you still own it)
- **Mutable borrow (`&mut`)** = Let someone rearrange items in your box (exclusive access)
- **Clone** = Make a photocopy of the box contents (two boxes now)
- **Drop** = Box goes in the trash (end of scope or explicit drop)

</mental_model>

<context_gathering>

**MANDATORY — do this BEFORE tracing ownership**

1. **Locate the code** — If $ARGUMENTS is a file:line or function name, use Read/Grep/Glob to find the actual code. If it's a code snippet, use it directly.

2. **Find where the value is created** — Trace the origin: constructor, function return, or literal. Read the creating function if it's external.

3. **Find where the value is consumed** — Grep for the variable name across the function/module to find all moves, borrows, and the final drop point.

4. **Read callers and callees** — Check functions that receive ownership or borrows of the value to understand the full transfer chain.

5. **Check Copy/Clone implementations** — Grep for `impl Copy` or `#[derive(Copy, Clone)]` on the type to know if assignments are moves or copies.

Only proceed after understanding the value's full lifecycle in the real code.
</context_gathering>

<process>

1. **Identify Owned Values** - What variables own heap data?

2. **Track Transfers** - For each line:
   - Is ownership moving?
   - Is a borrow being created?
   - Is data being cloned?

3. **Build Flow Diagram**:
   ```
   let s1 = String::from("hello");
       │
       │ s1 OWNS the String
       │
       ▼ MOVE
   let s2 = s1;
       │
       │ s2 OWNS the String now
       × s1 INVALID (moved from)
       │
       ▼ BORROW
   let len = s2.len();  // &self, temporary borrow
       │
       │ s2 still OWNS
       │
       ▼ DROP
   } // s2 dropped, String deallocated
   ```

4. **Mark Invalid Points** - Where does each variable become unusable?

5. **Identify Drop Points** - When is memory freed?

</process>

<output_format>

### OWNERSHIP SUMMARY

| Variable | Type | Owns Heap? | Fate |
|----------|------|------------|------|
| s1 | String | Yes | Moved to s2 |
| s2 | String | Yes | Dropped at line X |
| r | &String | No (borrow) | Ends at line Y |

### FLOW DIAGRAM

```
LINE │ CODE                    │ OWNERSHIP STATE
─────┼─────────────────────────┼───────────────────────
  1  │ let s1 = String::new(); │ s1: OWNER ●
  2  │ let s2 = s1;            │ s1: ✗ MOVED
     │                         │ s2: OWNER ●
  3  │ let r = &s2;            │ s2: OWNER ● ←─┐
     │                         │ r:  BORROW ───┘
  4  │ println!("{}", r);      │ r: last use
  5  │ drop(s2);               │ s2: DROPPED ○
  6  │ }                       │ (scope end)
```

### VISUAL OWNERSHIP TRANSFER

```
        s1                    s2
        │                     │
        ● String::from()      │
        │                     │
        │─────MOVE───────────►●
        ×                     │
      (invalid)               │
                              │
                        &s2 ──┤ BORROW
                              │
                              ● still valid
                              │
                              ▼
                         DROP ○
```

### COPY vs MOVE ANALYSIS

| Type | Behavior | Why |
|------|----------|-----|
| `i32`, `bool`, `char` | Copy | Implements `Copy`, stack-only |
| `String`, `Vec`, `Box` | Move | Heap data, no `Copy` |
| `&T` | Copy | References are `Copy` |

### DROP TIMELINE

```
Scope {
    s1 created ─────────────────┐
    s2 created ──────────────┐  │
    r borrowed ──────────┐   │  │
                         │   │  │
    r last used ─────────┘   │  │
    s2 dropped ──────────────┘  │
    s1 already moved ───────────× (no drop needed)
}
```

### POTENTIAL ISSUES

Flag any of these if detected:
- **Use-after-move:** Value used after being moved (e.g., `s1` used after move at line X)
- **Dangling reference:** Borrow outlives the owner (e.g., `r` outlives `s2`)
- **Double free:** Multiple owners trying to drop the same data

</output_format>

<patterns_reference>

### Pattern: Move
```rust
let a = String::from("x");
let b = a;      // a moved to b
// a is now invalid
```

### Pattern: Clone
```rust
let a = String::from("x");
let b = a.clone();  // Deep copy
// Both a and b valid
```

### Pattern: Borrow
```rust
let a = String::from("x");
let b = &a;     // Borrow, a still owns
// Both usable, b is temporary view
```

### Pattern: Transfer into Function
```rust
fn take(s: String) { }  // Takes ownership

let a = String::from("x");
take(a);        // a moved into function
// a invalid
```

### Pattern: Borrow into Function
```rust
fn look(s: &String) { }  // Borrows

let a = String::from("x");
look(&a);       // Temporary borrow
// a still valid
```

### Pattern: Return Ownership
```rust
fn give() -> String {
    String::from("x")  // Ownership transferred to caller
}

let a = give();  // a now owns the String
```

</patterns_reference>

<rules>

- ALWAYS trace to the drop point
- Mark moves clearly (× for invalid)
- Distinguish Copy types (no move, just copy)
- Show borrow lifetimes as spans
- Identify any use-after-move errors

</rules>

<success_criteria>
- Ownership summary table is complete for all variables
- Flow diagram traces every ownership transfer line-by-line
- Visual ownership transfer diagram is clear and accurate
- Copy vs Move types are correctly identified
- Drop timeline shows correct deallocation order
- All potential issues (use-after-move, dangling refs) are flagged
</success_criteria>

</ownership-flow-skill>
