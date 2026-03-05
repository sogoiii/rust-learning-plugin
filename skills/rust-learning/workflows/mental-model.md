<mental-model-skill>

<task>
Explain the provided Rust code to a senior developer learning Rust. Provide the **correct mental model** - assume no prior understanding.
</task>

<input>

```rust
$ARGUMENTS
```

Depth: standard (unless user specifies "brief" or "deep")
</input>

<verification_workflow>
Before writing ANY explanation:

1. **Identify** - What construct is this? (macro, trait, struct, fn, etc.)

2. **Research in PARALLEL** - Launch these subagents concurrently using Task tool:

   **Subagent A: Web Research** (subagent_type: general-purpose)
   - Launch PARALLEL searches when multiple queries would help:
     - Official Rust docs (std library, language reference)
     - Crate docs (crates.io, docs.rs) if external crate
     - Common patterns/gotchas (blog posts, Stack Overflow)
   - Use `mcp__exa__web_search_exa` for each search
   - Use `mcp__context7__resolve-library-id` + `mcp__context7__query-docs` if it's a crate
   - Return: key findings, doc links, verified behavior

   **Subagent B: Local Codebase Context** (subagent_type: Explore)
   - Search current directory for usages of the code/construct being explained
   - Find how it's used in context (callers, related types, imports)
   - Identify project-specific patterns that might inform the explanation
   - Return: usage examples from this codebase, related code locations

3. **Synthesize** - Combine web research + local context for complete picture
4. **Verify** - Cross-reference behavior, don't assume

If verification fails, prepend:
> **Could not verify online. Manual review recommended.**

Still provide your answer.
</verification_workflow>

<output_format>

### WHAT THIS IS
Brief: 1-2 sentences. Standard: 1-2 paragraphs covering what this construct is, what problem it solves, when to use it.

### HOW IT WORKS
Brief: 1-2 sentences. Standard: 1-2 paragraphs covering mechanics — compile-time vs runtime, what gets generated/expanded, execution flow.

### MEMORY & LIFETIME
Brief: 1-2 sentences. Standard: 1 paragraph covering stack vs heap, lifetime annotations, allocation/freeing, ownership implications.

### USAGE PATTERNS
Brief: 1 minimal example. Standard: 2-3 common usage patterns with VALID, RUNNABLE code snippets.

### GOTCHAS
Brief: 1-2 bullet points. Standard: 3-5 bullet points covering common mistakes, surprises, things that confuse newcomers.

### DEEP DIVE (only if depth is "deep")
Include: macro expansion (if applicable), memory layout, compiler behavior, comparison to alternatives, performance characteristics.
</output_format>

<rules>
- Code snippets MUST be valid, compilable Rust
- Never use pseudocode
- Explain lifetimes in plain English, then show notation
- If something happens at compile-time vs runtime, be explicit
- Correct common misconceptions directly
</rules>

<success_criteria>
- Correct mental model is provided, not just syntax description
- All code snippets are valid, compilable Rust
- Lifetimes explained in plain English before notation
- Compile-time vs runtime distinction is explicit where relevant
- Common misconceptions are directly addressed
- Depth level (brief/standard/deep) is respected in output length
- Web research and local codebase context are both consulted
</success_criteria>

</mental-model-skill>
