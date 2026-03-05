---
name: rust-learning
description: Interactive Rust learning tools for visualizing and explaining ownership, borrowing, lifetimes, type system, async, macros, patterns, compiler errors, and idiomatic style variations. Use when the user wants to understand Rust concepts, debug borrow checker issues, or learn Rust patterns.
---

<essential_principles>
<teaching_approach>
<principle name="visualize-first">ASCII diagrams, timelines, and flow charts make abstract concepts concrete</principle>
<principle name="mental-models">Every concept gets an analogy (library cards for borrows, leases for lifetimes, cookie cutters for generics)</principle>
<principle name="show-the-why">Don't just explain what happens; explain WHY Rust requires it</principle>
<principle name="valid-code-only">All code snippets must be compilable Rust, never pseudocode</principle>
<principle name="research-first">Use web search and codebase exploration to verify behavior before teaching</principle>
</teaching_approach>
<shared_references>
<reference path="references/borrow-ownership-rules.md">Core borrow checker rules, NLL, Copy vs Move types</reference>
</shared_references>
</essential_principles>

<intake>
What would you like to explore?

**Compiler Errors:**
1. **error** — Decode compiler errors with plain English + fix patterns

**Ownership & Memory:**
2. **trace-borrow** — Analyze borrow timeline (when borrows start/end, conflict zones)
3. **ownership-flow** — Visualize ownership transfers (move/borrow/clone diagrams)
4. **mental-model** — Explain any Rust construct with correct mental model

**Idiomatic Style:**
5. **variations** — Show multiple ways to write the same code with tradeoff explanations

**Ownership & Memory (continued):**
6. **why-clone** — Analyze if .clone() is necessary, suggest alternatives
7. **trace-lifetimes** — Visualize lifetime annotations with scope diagrams

**Code Explanation:**
8. **explain** — Explain a function signature in detail
9. **trait-bounds** — Decode complex trait bounds and where clauses

**Async:**
10. **trace-async** — Trace async execution flow with state machine diagrams

**Safety & Testing:**
11. **test-scaffold** — Generate test scaffold with todo!() stubs
12. **assess-finding** — Analyze a code suggestion/finding with diagrams

**Macros:**
13. **expand-macro** — Break down macro_rules! or derive macros

**Pattern Matching:**
14. **pattern-match** — Pattern matching deep dive (exhaustiveness, binding modes, guards)

**Type System & Generics:**
15. **smart-pointer** — Decision tree for Box, Rc, Arc, RefCell, Mutex
16. **compare-dispatch** — Static dispatch vs dynamic dispatch tradeoffs

**Safety:**
17. **unsafe-audit** — Audit unsafe code for invariants and UB risks

<wait_for_response/>
</intake>

<routing>
| Response | Workflow |
|----------|----------|
| 1, "error", "E0", "compiler error" | `workflows/error.md` |
| 2, "trace-borrow", "borrow" | `workflows/trace-borrow.md` |
| 3, "ownership-flow", "ownership", "move" | `workflows/ownership-flow.md` |
| 4, "mental-model", "what is" | `workflows/mental-model.md` |
| 5, "variations", "other ways", "idiomatic", "rewrite", "alternative" | `workflows/variations.md` |
| 6, "why-clone", "clone" | `workflows/why-clone.md` |
| 7, "trace-lifetimes", "lifetime" | `workflows/trace-lifetimes.md` |
| 8, "explain", "function signature" | `workflows/explain.md` |
| 9, "trait-bounds", "bounds", "where clause" | `workflows/trait-bounds.md` |
| 10, "trace-async", "async", "await", "future" | `workflows/trace-async.md` |
| 11, "test-scaffold", "test", "scaffold" | `workflows/test-scaffold.md` |
| 12, "assess-finding", "finding", "suggestion" | `workflows/assess-finding.md` |
| 13, "expand-macro", "macro", "derive" | `workflows/expand-macro.md` |
| 14, "pattern-match", "match", "pattern" | `workflows/pattern-match.md` |
| 15, "smart-pointer", "Box", "Rc", "Arc", "RefCell", "Mutex" | `workflows/smart-pointer.md` |
| 16, "compare-dispatch", "dispatch", "dyn", "impl Trait" | `workflows/compare-dispatch.md` |
| 17, "unsafe-audit", "unsafe" | `workflows/unsafe-audit.md` |

**After reading the workflow, follow it exactly.**

**Direct invocation:** When invoked via slash command with a specific workflow name (e.g., "mental-model $ARGUMENTS"), skip the intake question and route directly to the named workflow.
</routing>

<reference_index>
All shared knowledge in `references/`:

**Borrow & Ownership:** borrow-ownership-rules.md — Core rules, NLL behavior, Copy vs Move types, common patterns
</reference_index>

<workflows_index>
| Workflow | Purpose |
|----------|---------|
| error.md | Decode Rust compiler errors |
| trace-borrow.md | Analyze borrow timeline with conflict zones |
| ownership-flow.md | Visualize ownership transfers and drops |
| mental-model.md | Explain Rust constructs with correct mental model |
| variations.md | Show multiple idiomatic ways to write the same code |
| why-clone.md | Assess .clone() necessity and alternatives |
| trace-lifetimes.md | Visualize lifetime annotations and scope diagrams |
| explain.md | Break down function signatures |
| trait-bounds.md | Decode trait bounds and where clauses |
| trace-async.md | Trace async execution flow |
| test-scaffold.md | Generate test scaffolds with todo!() stubs |
| assess-finding.md | Analyze code suggestions/findings |
| expand-macro.md | Break down macro_rules! and derive macros |
| pattern-match.md | Pattern matching deep dive |
| smart-pointer.md | Decision tree for smart pointer selection |
| compare-dispatch.md | Compare static vs dynamic dispatch |
| unsafe-audit.md | Audit unsafe code blocks |
</workflows_index>

<success_criteria>
- User understands the Rust concept they asked about
- Explanation includes visual diagrams where applicable
- Code examples are valid, compilable Rust
- Mental model provided for the concept
- Common gotchas highlighted
</success_criteria>
