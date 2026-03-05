<decode-error-skill>

<task>
Decode and explain the Rust compiler error so the user deeply understands WHY it occurred and HOW to fix it.
</task>

<input>

```
$ARGUMENTS
```
</input>

<context_gathering>

**MANDATORY — do this BEFORE analysis**

1. **Extract file locations** — Parse all `file:line` references from the error (e.g., `src/routes/login.rs:103`, `src/domain/data_stores.rs:21`).

2. **Read the error site** — Use the Read tool to read the file containing the error. Read the full function where the error occurs plus ~30 lines of surrounding context.

3. **Read referenced files** — If the error references other files (trait definitions, type definitions, other impls), read those too. Follow the chain:
   - Type mismatch? Read the expected type's definition.
   - Trait method error? Read the trait definition.
   - Lifetime/borrow error spanning files? Read both files.

4. **Resolve types** — If the error involves types that aren't obvious from context:
   - Grep for struct/enum/trait definitions mentioned in the error.
   - Check type aliases (`type Foo = ...`) that may obscure the real type.
   - For generic types, find the concrete instantiation.

5. **Find working examples** — Grep the codebase for similar patterns that compile successfully. This helps identify what's different about the failing code.

Only proceed to analysis after gathering sufficient context. Do NOT guess at code you haven't read.
</context_gathering>

<process>

1. **Identify Error Code** - Extract E0xxx code if present. Look up official explanation via `rustc --explain E0xxx` mentally.

2. **Plain English Translation** - Translate the error into simple language FIRST:
   > **Summary:** [One sentence explaining what went wrong in human terms]

3. **Locate the Conflict** - For borrow/lifetime errors, identify:
   - The Owner (what data is being accessed)
   - The First Borrow (when/where reference was created)
   - The Conflicting Action (what operation violated the rules)

4. **Annotated Code** - Show the code with inline comments marking:
   ```rust
   let mut data = vec![1, 2, 3];
   let first_ref = &data;      // <-- Immutable borrow starts here
   data.push(4);               // <-- CONFLICT: needs &mut while &data active
   println!("{}", first_ref);  // <-- Immutable borrow still needed here
   ```

5. **Timeline Diagram** (for borrow/lifetime errors):
   ```
   LINE 2: data created ─────────────────────────────┐ owner
   LINE 3: &data borrowed (immutable) ──────────┐    │
   LINE 4: data.push() CONFLICT ────────────────┼────┤
   LINE 5: first_ref used ──────────────────────┘    │
   LINE 6: } data dropped ──────────────────────────┘
   ```

6. **Core Rule Violated** - State which Rust rule was broken:
   - Aliasing rule (one &mut OR many &, not both)
   - Move semantics (value used after move)
   - Lifetime rule (reference outlives data)
   - etc.

7. **Fix Patterns** - Provide 2-3 concrete solutions:
   | Pattern | When to Use | Code |
   |---------|-------------|------|
   | Reorder | Mutation doesn't need old state | `data.push(4); let r = &data;` |
   | Clone | Need independent copy | `let r = data.clone();` |
   | Scope | Limit borrow lifetime | `{ let r = &data; } data.push(4);` |
</process>

<common_error_categories>

### Borrow Checker (E0502, E0503, E0505, E0506)
- Focus on timeline of borrows
- Show conflict zone visually
- Suggest reordering or scoping

### Move Errors (E0382, E0507)
- Show ownership transfer point
- Explain why value is invalidated
- Suggest Clone, references, or Rc/Arc

### Lifetime Errors (E0106, E0597, E0621)
- Draw scope diagram
- Explain which reference outlives which data
- Show lifetime annotation solutions

### Type Errors (E0308, E0277)
- Show expected vs actual type
- Explain trait bounds if applicable
- Suggest conversions or implementations
</common_error_categories>

<output_format>

### PLAIN ENGLISH
[One sentence summary - no jargon]

### WHAT HAPPENED
[2-3 sentences explaining the sequence of events]

### ANNOTATED CODE
```rust
[Code with inline conflict markers]
```

### TIMELINE (if applicable)
```
[ASCII timeline showing borrow/lifetime spans]
```

### RULE VIOLATED
> [State the Rust rule in plain terms]

### FIX OPTIONS
[2-3 concrete fixes with code examples]
</output_format>

<rules>
- **NEVER write, edit, or modify any files.** This command is READ-ONLY. Your job is to explain and suggest — the user will make changes themselves.
- **NEVER use Edit, Write, NotebookEdit, or Bash tools.** Only Read, Grep, and Glob are allowed.
- Show fix suggestions as code blocks in your response. Do NOT apply them.
- ALWAYS start with plain English - jargon comes after understanding
- Use the ACTUAL code from the error, not generic examples
- If error message is incomplete, ask for full output
- Prefer fixes that teach good patterns, not just workarounds
</rules>

<success_criteria>
- Error site and all referenced files read before analysis begins
- Plain English summary provided before any technical explanation
- Annotated code uses actual code from the error, not generic examples
- Timeline diagram included for borrow/lifetime errors
- Core Rust rule violated is clearly stated
- At least 2 concrete fix options with code examples provided
- No files written, edited, or modified
</success_criteria>

</decode-error-skill>
