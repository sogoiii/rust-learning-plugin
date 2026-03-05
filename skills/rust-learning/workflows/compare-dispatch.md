<compare-dispatch-skill>

<task>
Explain the difference between static dispatch (generics, `impl Trait`) and dynamic dispatch (`dyn Trait`), helping the user choose the right approach for their situation.
</task>

<input>
If $ARGUMENTS is provided, analyze that specific context. If empty, provide a general comparison.

Context: $ARGUMENTS
</input>

<mental_models>
**Static Dispatch (Generics)** = Cookie Cutter
- Compiler stamps out a specialized version for each type
- Fast: no runtime lookup, can inline
- Binary bloat: more code generated

**Dynamic Dispatch (Trait Objects)** = Universal Remote
- Single function, looks up method at runtime via vtable
- Flexible: can mix types in collections
- Slight overhead: pointer indirection
</mental_models>

<context_gathering>

**MANDATORY — do this BEFORE comparing dispatch approaches**

1. **Locate the code** — If $ARGUMENTS is a file:line or function name, use Read/Grep/Glob to find the actual code. If it's a context description, use it directly.

2. **Search for existing trait objects** — Grep for `dyn `, `Box<dyn`, `&dyn` across the codebase to see where dynamic dispatch is already used and why.

3. **Search for existing generic impls** — Grep for `impl<T`, `<T:`, `impl Trait` to see where static dispatch is preferred in the project.

4. **Check the trait definition** — If a specific trait is mentioned, read its definition and check object safety (no `Self` returns, no generic methods).

5. **Read the usage context** — Understand whether this is a hot path, a collection of mixed types, or a plugin boundary to inform the dispatch recommendation.

Only proceed after understanding the project's existing dispatch patterns and the specific use case.
</context_gathering>

<core_comparison>
| Aspect | Static (`impl Trait` / `<T: Trait>`) | Dynamic (`dyn Trait`) |
|--------|--------------------------------------|----------------------|
| **Resolution** | Compile-time | Runtime |
| **Mechanism** | Monomorphization | VTable pointer |
| **Performance** | Faster (inlining possible) | Slight overhead (~ns) |
| **Binary Size** | Larger (code duplication) | Smaller |
| **Compile Time** | Slower (more code gen) | Faster |
| **Flexibility** | Same type only | Mixed types OK |
| **Sized?** | `T: Sized` by default | `dyn Trait` is `!Sized` |
</core_comparison>

<syntax_comparison>
### Static Dispatch
```rust
// Generic parameter
fn process<T: Display>(item: T) { ... }

// impl Trait (argument position)
fn process(item: impl Display) { ... }

// impl Trait (return position) - single concrete type
fn make_iter() -> impl Iterator<Item = i32> { ... }
```

### Dynamic Dispatch
```rust
// Trait object (reference)
fn process(item: &dyn Display) { ... }

// Trait object (boxed - owned)
fn process(item: Box<dyn Display>) { ... }

// Heterogeneous collection
let items: Vec<Box<dyn Display>> = vec![
    Box::new(42),
    Box::new("hello"),
];
```
</syntax_comparison>

<decision_tree>
```
Do you need a collection of DIFFERENT types?
│
├─ YES → Use `dyn Trait`
│        (Vec<Box<dyn Draw>>, HashMap<K, Box<dyn Handler>>)
│
└─ NO → Is performance critical?
        │
        ├─ YES → Use generics / `impl Trait`
        │        (zero-cost abstraction, inlining)
        │
        └─ NO → Either works, prefer generics for clarity
                (unless binary size is a concern)
```
</decision_tree>

<when_to_use>
### Use Static Dispatch When:
- Performance matters (hot paths, tight loops)
- Working with single concrete type
- Writing library code (let user choose type)
- Return type is known but complex (`-> impl Iterator`)
- Need `Sized` types

### Use Dynamic Dispatch When:
- Heterogeneous collections (different types, same behavior)
- Plugin systems (types not known at compile time)
- Reducing binary size / compile time
- Object-safe traits only
- GUI elements, event handlers, strategy pattern
</when_to_use>

<object_safety>
Not all traits can be made into `dyn Trait`. A trait is **object-safe** if:
- No `Self: Sized` bound
- No associated functions (only methods with `&self`/`&mut self`)
- No generic methods
- No use of `Self` in return position

```rust
// Object-safe
trait Draw {
    fn draw(&self);
}

// NOT object-safe (returns Self)
trait Clone {
    fn clone(&self) -> Self;
}
```
</object_safety>

<performance>
```
Operation          │ Static │ Dynamic │ Difference
───────────────────┼────────┼─────────┼───────────
Method call        │ ~0ns   │ ~1-2ns  │ vtable lookup
Can inline?        │ Yes    │ No      │ major for hot paths
Cache behavior     │ Better │ Worse   │ code locality
```

For most applications, the difference is negligible. Only optimize if profiling shows it matters.
</performance>

<code_examples>
If $ARGUMENTS references a specific trait or function, include:

### Your Specific Case

[Analyze the provided trait/function and recommend approach]

### Example: Shape Drawing

**Static (when types known):**
```rust
fn draw_all<T: Draw>(shapes: &[T]) {
    for shape in shapes {
        shape.draw();  // Monomorphized per type
    }
}
// Must call with single type: draw_all(&circles)
```

**Dynamic (when types mixed):**
```rust
fn draw_all(shapes: &[Box<dyn Draw>]) {
    for shape in shapes {
        shape.draw();  // VTable lookup
    }
}
// Can mix: draw_all(&[Box::new(Circle), Box::new(Square)])
```
</code_examples>

<output_format>
### RECOMMENDATION FOR YOUR CASE
[If context provided, give specific advice]

### COMPARISON TABLE
[Include the core comparison table]

### CODE EXAMPLE
[Show both approaches for their use case]

### GOTCHAS
- `dyn Trait` requires `Box`, `&`, or `Arc` (not `Sized`)
- `impl Trait` in return position = exactly ONE concrete type
- Can't mix `impl Trait` in collections
</output_format>

<success_criteria>
- Both dispatch mechanisms are clearly explained with concrete examples
- A specific recommendation is given when user context is provided
- Object safety rules are mentioned when relevant
- Performance tradeoffs are quantified, not just described qualitatively
- User can confidently choose between static and dynamic dispatch for their case
</success_criteria>

</compare-dispatch-skill>
