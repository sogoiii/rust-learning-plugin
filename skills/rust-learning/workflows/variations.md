<variations-skill>

<task>
Show multiple idiomatic ways to write the same code. For each variation, explain what changed, why someone would choose it, and what tradeoffs it introduces. This is a learning tool — NO files are modified.
</task>

<input>

```rust
$ARGUMENTS
```

If $ARGUMENTS is a file:line or function name, read that code first.
</input>

<mental_model>

## "There's More Than One Way to Say It"

Rust has a rich surface syntax. The same logic can be expressed with:
- `if let` vs `match` vs `.map()` chains
- Early return vs nested blocks vs `?` operator
- Explicit types vs type inference vs turbofish
- Imperative loops vs iterator chains
- `unwrap_or` vs `unwrap_or_else` vs `match` on Option/Result

Each form has a context where it shines. Idiomatic Rust isn't one style — it's knowing WHICH style fits the situation.

</mental_model>

<context_gathering>

**MANDATORY — do this BEFORE generating variations**

1. **Locate the code** — If $ARGUMENTS is a file:line or function name, use Read/Grep/Glob to find the actual code. Work with real code, not assumptions.

2. **Understand the intent** — What is this code trying to accomplish? Identify the core goal before rewriting.

3. **Identify the current style** — What patterns does the original use? (match, if/else, loops, method chains, explicit returns, etc.)

4. **Check codebase conventions** — Grep for similar patterns in the project. Does the codebase lean toward one style? Note this for context.

5. **Identify variation axes** — Which of these apply?
   - Control flow (match vs if let vs combinators)
   - Error handling (? vs match vs map_err vs unwrap_or)
   - Iteration (for loop vs .iter().map() vs .fold())
   - Expression style (explicit return vs tail expression)
   - Type handling (turbofish vs let binding vs inference)
   - Ownership (borrow vs clone vs move)

Only proceed after understanding the original code's intent and structure.
</context_gathering>

<process>

1. **Present the original** — Show the code as-is with a brief summary of what it does.

2. **Generate 3-5 variations** — Each variation should:
   - Be valid, compilable Rust (no pseudocode)
   - Preserve the exact same behavior
   - Change the style/approach, not the logic
   - Range from "more explicit" to "more concise"

3. **For each variation, explain:**
   - **What changed** — The specific syntactic/structural difference
   - **Why choose this** — When this form is the better choice
   - **Tradeoff** — What you gain and what you lose

4. **Rank idiomaticity** — After all variations, note which version experienced Rustaceans would most likely write in production and why. Be honest — sometimes the verbose version IS the idiomatic one.

5. **Highlight Rust-specific patterns** — Call out patterns that are unique to Rust or surprising to people from other languages (e.g., tail expressions, `?` chaining, `let else`).

</process>

<variation_categories>

### Control Flow
| From | To | When Better |
|------|----|-------------|
| `match` with 2 arms | `if let` / `else` | Only care about one variant |
| `if let` | `let else` | Need to early-return on mismatch |
| Nested `if`/`else` | `match` | 3+ branches, especially on enums |
| `match` returning value | `.map()` / `.and_then()` | Chaining Option/Result transforms |

### Error Handling
| From | To | When Better |
|------|----|-------------|
| `match Ok/Err` | `?` operator | Propagating errors up |
| `.unwrap()` | `?` or `match` | Production code (unwrap panics) |
| `.unwrap_or(default)` | `.unwrap_or_else(|| ...)` | Default is expensive to compute |
| Multiple `?` lines | `.and_then()` chain | Transforming through a pipeline |
| `if err != nil` style | Rust `Result` combinators | Coming from Go |

### Iteration
| From | To | When Better |
|------|----|-------------|
| `for` loop + `push` | `.iter().map().collect()` | Transforming collections |
| `.iter().for_each()` | `for` loop | Side effects (more readable) |
| Manual accumulator | `.fold()` / `.reduce()` | Aggregation |
| Index-based loop | `.iter().enumerate()` | Need both index and value |

### Expression Style
| From | To | When Better |
|------|----|-------------|
| `return value;` | Tail expression (no semicolon) | Last expression in block |
| `let x = if ... { } else { }` | `match` | More than 2 branches |
| Temporary variable | Inline expression | Simple, one-use values |
| Multiple statements | Method chain | Linear data transformation |

</variation_categories>

<output_format>

## ORIGINAL

```rust
[The original code]
```

**What it does:** [1-sentence summary]

---

## VARIATION 1: [Short label, e.g., "Using `if let`"]

```rust
[Rewritten code]
```

| Aspect | Detail |
|--------|--------|
| **What changed** | [Specific change description] |
| **Why choose this** | [When this version is better] |
| **Tradeoff** | [What you gain vs what you lose] |

---

## VARIATION 2: [Short label]

```rust
[Rewritten code]
```

| Aspect | Detail |
|--------|--------|
| **What changed** | [Specific change description] |
| **Why choose this** | [When this version is better] |
| **Tradeoff** | [What you gain vs what you lose] |

---

[... more variations ...]

---

## IDIOMATICITY RANKING

| Rank | Variation | Best When |
|------|-----------|-----------|
| 1 | [Most idiomatic] | [Context] |
| 2 | ... | ... |
| 3 | ... | ... |

**In this codebase:** [Note if the project's style leans toward one approach]

</output_format>

<rules>
- **NEVER write, edit, or modify any files.** This command is READ-ONLY. Your job is to show alternatives — the user will choose and apply themselves.
- **NEVER use Edit, Write, NotebookEdit, or Bash tools.** Only Read, Grep, and Glob are allowed.
- Show all variations as code blocks in your response. Do NOT apply them.
- Every variation MUST compile. No pseudocode, no hand-waving.
- Preserve exact behavior — variations change form, not function.
- Include at least 3 variations, max 5. Quality over quantity.
- Don't force conciseness — sometimes the explicit version IS better. Say so.
- If the original is already the most idiomatic form, say that. Don't invent variations just to fill space.
- Always explain WHY someone would choose each version, not just show it.
</rules>

<success_criteria>
- Original code located and understood before generating variations
- 3-5 valid, compilable variations shown
- Each variation has: what changed, why choose it, tradeoff
- Idiomaticity ranking provided with context
- Codebase conventions noted if relevant
- No files written, edited, or modified
</success_criteria>

</variations-skill>
