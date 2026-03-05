<trait-bounds-skill>

<task>
Decode complex trait bounds so the user understands what each bound requires and why.
</task>

<input>
```rust
$ARGUMENTS
```
</input>

<mental_model>
## Capability Contracts

Trait bounds are like job requirements:
- `T: Clone` = "T must be able to duplicate itself"
- `T: Send` = "T must be safe to send to another thread"
- `T: 'static` = "T must not contain any borrowed references (or only 'static ones)"

Combined bounds compound requirements: `T: Clone + Send + 'static` = all three requirements.
</mental_model>

<context_gathering>

**MANDATORY — do this BEFORE decoding trait bounds**

1. **Locate the code** — If $ARGUMENTS is a file:line or function name, use Read/Grep/Glob to find the actual code. If it's a code snippet, use it directly.

2. **Find the trait definitions** — For each trait in the bounds, Grep/Glob for its definition (in the codebase or identify it as a std/external trait). Read its required methods and associated types.

3. **Check which types implement the traits** — Grep for `impl TraitName for` to see concrete implementations and understand why the bound exists.

4. **Read the function body** — Read the bounded function's body to see WHERE each trait bound is actually used (which method call, which conversion).

5. **Check callers** — Grep for call sites to see what concrete types are passed, confirming the bounds are exercised.

Only proceed after understanding what each trait requires and why the function needs it.
</context_gathering>

<process>
1. **Parse the Bounds** - Identify all trait constraints

2. **Annotate Each Bound**:
   ```rust
   fn process<T>(item: T) -> Result<(), Error>
   where
       T: Clone + Send + 'static,
       // ─────┬─ ────┬─ ────┬────
       //      │      │      │
       //      │      │      └── T owns all its data (no borrowed refs)
       //      │      └── T can be sent to another thread
       //      └── T can be duplicated
   ```

3. **Explain WHY Each Bound** - What does the function need to do that requires this?

4. **Identify Simplifications** - Could any bounds be removed?
</process>

<output_format>
### BOUNDS BREAKDOWN

```rust
[Annotated signature with bounds explained]
```

### BOUND-BY-BOUND EXPLANATION

| Bound | Meaning | Why Needed |
|-------|---------|------------|
| `Clone` | Can call `.clone()` on T | [reason from function body] |
| `Send` | Safe to transfer between threads | [using in spawn/channel] |
| `'static` | No borrowed data | [stored beyond current scope] |

### COMMON BOUNDS REFERENCE

| Bound | What It Means | Common Use |
|-------|---------------|------------|
| `Clone` | `.clone()` available | Duplicate data |
| `Copy` | Bitwise copyable, `Clone` implied | Small values (i32, bool) |
| `Send` | Safe to send to another thread | Thread spawning, channels |
| `Sync` | Safe to share references across threads | `&T` is `Send` if `T` is `Sync` |
| `'static` | Owns all data or refs are `'static` | Thread spawning, storage |
| `Sized` | Size known at compile time | Default, almost everything |
| `?Sized` | May be unsized | Accepting `str`, `[T]`, `dyn Trait` |
| `Debug` | `{:?}` formatting | Error messages, logging |
| `Display` | `{}` formatting | User-facing output |
| `Default` | Has default value | Builder patterns, Option unwrap |
| `Eq` / `PartialEq` | Equality comparison | HashMap keys, assertions |
| `Ord` / `PartialOrd` | Ordering comparison | Sorting, BTreeMap keys |
| `Hash` | Can be hashed | HashMap/HashSet keys |
| `Iterator` | Can produce sequence | for loops, collect() |
| `Into<X>` / `From<X>` | Type conversion | Flexible function arguments |
| `AsRef<X>` | Cheap reference conversion | Accept `&str` or `&String` |

### LIFETIME BOUNDS

| Bound | Meaning |
|-------|---------|
| `T: 'a` | T must be valid for at least lifetime 'a |
| `T: 'static` | T contains no non-static references |
| `'a: 'b` | Lifetime 'a outlives 'b |

### COMPOUND BOUNDS EXPLAINED

**`T: Clone + Send + 'static`** (common for thread spawning)
```
T: Clone      → Need to duplicate for the new thread
T: Send       → Safe to move to another thread
T: 'static    → No borrowed data that might become invalid
```

**`T: Iterator<Item = U> + 'a`**
```
T: Iterator   → Produces a sequence
Item = U      → Each element is type U
'a            → Iterator valid for lifetime 'a
```

**`F: FnOnce() -> R + Send + 'static`** (common for spawn)
```
F: FnOnce()   → Callable exactly once
-> R          → Returns type R
Send          → Safe to send to another thread
'static       → No borrowed captures
```

### SIMPLIFICATION ANALYSIS

If bounds could be simplified, show examples:
- `T: Clone + Copy` → Just `T: Copy` (Copy implies Clone)
- `T: Ord` → Implies `PartialOrd + Eq + PartialEq`

If all bounds are necessary, state: "All bounds appear necessary for this function's requirements."

### WHERE CLAUSE vs INLINE

Two equivalent syntaxes:
```rust
// Inline bounds
fn foo<T: Clone + Send>(x: T) { }

// Where clause (cleaner for complex bounds)
fn foo<T>(x: T)
where
    T: Clone + Send,
{ }
```

Use `where` when:
- Multiple type parameters with bounds
- Bounds are long
- Need bounds on associated types: `where I::Item: Debug`

### GOTCHAS

- `'static` doesn't mean "lives forever" - it means "CAN live forever" (no borrowed data)
- `Sync` and `Send` are auto-traits (usually derived automatically)
- `?Sized` is the only "negative" bound (opts OUT of Sized)
- `Copy` implies `Clone` but not vice versa
</output_format>

<rules>
- Explain EACH bound's purpose, not just definition
- Connect bounds to what the function does
- Suggest simplifications where possible
- Use the visual annotation style for complex signatures
</rules>

<success_criteria>
- Every trait bound in the input is identified and explained
- Each bound has a "why needed" rationale tied to the function body or context
- Simplification opportunities are flagged
- Visual annotation is provided for complex signatures
- User understands what they'd need to change to relax or tighten constraints
</success_criteria>

</trait-bounds-skill>
