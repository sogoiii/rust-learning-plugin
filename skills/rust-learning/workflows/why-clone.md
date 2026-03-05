<required_reading>
- references/borrow-ownership-rules.md
</required_reading>

<why-clone-skill>

<task>
Analyze whether `.clone()` is necessary, what alternatives exist, and what the cost is. Help the user avoid over-cloning while understanding when cloning is appropriate.
</task>

<input>

```rust
$ARGUMENTS
```

</input>

<mental_model>

## Clone is Not Evil, But...

Cloning is a valid tool, but new Rustaceans often clone to "shut up the borrow checker" without understanding the cost or alternatives. The goal:
- Clone when semantically correct (you NEED two independent copies)
- Avoid clone when restructuring would work
- Know the cost (cheap clone vs expensive clone)

</mental_model>

<context_gathering>

**MANDATORY — do this BEFORE analyzing clones**

1. **Locate the code** — If $ARGUMENTS is a file:line or function name, use Read/Grep/Glob to find the actual code. If it's a code snippet, use it directly.

2. **Find all uses of the cloned value** — Grep for the variable name in the function/module. Determine if the original is used after the clone.

3. **Check for Rc/Arc wrapping** — Grep the module for `Rc<`, `Arc<` on the cloned type. If already behind a smart pointer, the clone may be an `Rc::clone`.

4. **Read the function signatures** — Check if functions consuming the value take ownership (`T`) or borrow (`&T`). A clone for an `&T` parameter is avoidable.

5. **Check the type's Clone cost** — Read the struct definition to assess clone expense (heap allocations, nested collections, deep structures).

Only proceed after understanding what's being cloned, why, and how the value is used around it.
</context_gathering>

<process>

1. **Identify All Clones** - Find every `.clone()`, `.to_owned()`, `.to_string()`

2. **For Each Clone, Ask:**
   - Why is it needed? (borrow conflict? ownership transfer?)
   - Is it semantically correct? (do you NEED a copy?)
   - What's the cost? (size of data being cloned)
   - Is there an alternative? (reference, Rc/Arc, restructure)

3. **Classify the Clone:**
   | Classification | Description |
   |----------------|-------------|
   | **Necessary** | Semantically need independent copy |
   | **Defensive** | Avoiding complex lifetimes (acceptable) |
   | **Avoidable** | Could restructure to use references |
   | **Expensive** | Large data, consider Rc/Arc instead |

4. **Cost Analysis:**
   - Stack copy (trivial): `i32`, `bool`, `char`, `&T`
   - Small heap (cheap): Short strings, small vecs
   - Large heap (expensive): Large strings, big vecs, nested structures
   - Deep clone (very expensive): Trees, graphs, recursive structures

</process>

<output_format>

### CLONE INVENTORY

| Line | Expression | Type | Classification |
|------|------------|------|----------------|
| X | `s.clone()` | String | [Necessary/Avoidable/etc] |
| Y | `v.to_owned()` | Vec<T> | [Necessary/Avoidable/etc] |

### CLONE ANALYSIS

For each clone:

**Clone at line X: `expr.clone()`**
- **Why needed:** [borrow conflict / ownership transfer / etc]
- **Semantically correct?:** [Yes - need independent copy / No - just avoiding borrow checker]
- **Cost:** [Trivial / Cheap / Expensive]
- **Alternative:** [None / Use reference / Use Rc / Restructure]

### COST ASSESSMENT

```
Clone Cost Scale:
├── Trivial (Copy types)
│   └── i32, bool, &T, small tuples
├── Cheap (~ns)
│   └── Short strings (<100 chars), small vecs (<100 elements)
├── Moderate (~μs)
│   └── Medium strings/vecs, simple structs
├── Expensive (~ms for large data)
│   └── Large vecs, nested structures
└── Very Expensive
    └── Deep clones (trees), network resources
```

**Your clones:**
- `clone1`: [Cost level]
- `clone2`: [Cost level]

### ALTERNATIVES ANALYSIS

For each avoidable clone, show alternatives from the options below:

**Option 1: Use a reference**
```rust
// Before (clone)
fn process(s: String) { ... }
process(my_string.clone());

// After (borrow)
fn process(s: &String) { ... }  // or s: &str
process(&my_string);
```

**Option 2: Restructure for ownership**
```rust
// Before (clone to avoid move)
let a = expensive.clone();
use_expensive(expensive);
use_a(a);

// After (reorder)
use_a(&expensive);  // borrow first
use_expensive(expensive);  // then move
```

**Option 3: Use Rc/Arc for shared ownership**
```rust
// Before (multiple clones)
let a = expensive.clone();
let b = expensive.clone();

// After (shared ownership)
let shared = Rc::new(expensive);
let a = Rc::clone(&shared);  // Cheap! Just increments counter
let b = Rc::clone(&shared);
```

### VERDICT

| Clone | Keep? | Reason |
|-------|-------|--------|
| clone1 | ✓ Yes | Semantically need copy |
| clone2 | ✗ No | Can use reference instead |
| clone3 | ~ Maybe | Defensive clone, acceptable |

</output_format>

<idiomatic_patterns>

**When to Clone:**
- String from `&str` when you need ownership: `s.to_string()`
- Building data structures: `vec.push(item.clone())`
- Sending data to another thread (if not using Arc)

**When NOT to Clone:**
- Just to pass to a function (use `&`)
- To avoid thinking about lifetimes (restructure instead)
- Large data shared between components (use `Rc`/`Arc`)

**Cheap Clones (Always OK):**
- `Rc::clone()` / `Arc::clone()` - just increments counter
- `&str` → `String` for short strings
- Copy types (no clone needed, automatic)

</idiomatic_patterns>

<rules>

- Don't shame cloning - it's a valid tool
- Always assess the COST of the clone
- Suggest Rc/Arc for shared large data
- Show concrete refactoring examples
- Accept "defensive clones" as sometimes reasonable

</rules>

<success_criteria>
- Every clone/to_owned/to_string is inventoried
- Each clone is classified (Necessary/Defensive/Avoidable/Expensive)
- Cost assessment uses concrete scale with examples
- Avoidable clones have at least one concrete alternative with code
- Verdict table gives clear keep/remove recommendation
</success_criteria>

</why-clone-skill>
