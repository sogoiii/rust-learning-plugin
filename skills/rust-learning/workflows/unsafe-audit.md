<unsafe-audit-skill>

<task>
Audit unsafe code for correctness, identify invariants the programmer must maintain, and suggest safe abstractions where possible.
</task>

<input>

```rust
$ARGUMENTS
```
</input>

<mental_model>

**The Unsafe Contract**

`unsafe` is a contract between you and the compiler:
- **Compiler says:** "I can't verify this is safe, so I trust you."
- **You say:** "I promise to maintain the invariants that make this safe."

If you break the promise → **Undefined Behavior (UB)**.
</mental_model>

<context_gathering>

**MANDATORY — do this BEFORE auditing unsafe code**

1. **Locate the code** — If $ARGUMENTS is a file:line or function name, use Read/Grep/Glob to find the actual code. If it's a code snippet, use it directly.

2. **Find the surrounding safe API** — Read the full module to understand the safe public interface that wraps this unsafe code. Check if callers can violate invariants.

3. **Check invariants in comments** — Look for `// SAFETY:` comments above the unsafe block and `/// # Safety` doc comments on unsafe functions.

4. **Read related unsafe blocks** — Grep for other `unsafe` blocks in the same module to understand the overall safety story and shared invariants.

5. **Check test coverage** — Grep for `#[test]` and `miri` in the module's test files to see if the unsafe code is tested, especially under Miri.

Only proceed after understanding the unsafe block's context, its safe API boundary, and documented invariants.
</context_gathering>

<process>

1. **Identify Unsafe Operations** - Which superpower is being used?

2. **List Invariants** - What must be true for this to be safe?

3. **Check Each Invariant** - Is it guaranteed? Can it be violated?

4. **Identify UB Risks** - What could go wrong?

5. **Suggest Safe Alternatives** - Can this be made safe?
</process>

<output_format>

### UNSAFE OPERATIONS IDENTIFIED

| Line | Operation | Superpower Used |
|------|-----------|-----------------|
| X | `*ptr` | Raw pointer dereference |
| Y | `transmute` | Unsafe function call |

### INVARIANTS REQUIRED

For this code to be safe, the following MUST be true:

| Invariant | Verified? | How to Ensure |
|-----------|-----------|---------------|
| Pointer is non-null | [Yes/No/Unclear] | [Check or assumption] |
| Pointer is aligned | [Yes/No/Unclear] | [Check or assumption] |
| Pointer points to valid memory | [Yes/No/Unclear] | [Check or assumption] |
| No aliasing violations | [Yes/No/Unclear] | [Check or assumption] |
| Type is correctly initialized | [Yes/No/Unclear] | [Check or assumption] |

### UB RISK ANALYSIS

For each potential UB risk identified, include:

**Risk: [UB Type]**
- **What could happen:** [Description]
- **When it would occur:** [Trigger condition]
- **Severity:** [Instant crash / Silent corruption / Exploitable]
- **Mitigation:** [How to prevent]

### COMMON UB PATTERNS TO CHECK

| Pattern | UB Type | Check |
|---------|---------|-------|
| `*ptr` without null check | Null dereference | `if !ptr.is_null()` |
| `*ptr` after free | Use-after-free | Lifetime tracking |
| `&mut *ptr` aliasing | Mutable aliasing | Ensure exclusive access |
| Uninitialized read | Reading uninit memory | `MaybeUninit` |
| Bad transmute | Type confusion | Size and validity |
| Data race | Race condition | Synchronization |
| Out-of-bounds | Buffer overflow | Bounds checking |

### SAFE ABSTRACTION ANALYSIS

If the unsafe block CAN be wrapped in a safe abstraction, show:

```rust
// Before: raw unsafe
unsafe {
    [original code]
}

// After: safe wrapper
fn safe_wrapper(...) -> Result<T, Error> {
    // Validate invariants BEFORE unsafe
    if [invariant_check] {
        return Err(Error::InvalidInput);
    }

    // SAFETY: [document why this is now safe]
    // - Invariant 1: guaranteed by check above
    // - Invariant 2: guaranteed by type system
    unsafe {
        [original code]
    }
}
```

If the unsafe CANNOT be fully encapsulated, explain why and show best practice documentation:

```rust
/// # Safety
///
/// - `ptr` must be non-null and properly aligned
/// - `ptr` must point to a valid `T`
/// - No other references to the pointee may exist
unsafe fn my_unsafe_fn(ptr: *mut T) { ... }
```

### SAFETY COMMENT AUDIT

If a SAFETY comment exists, show it and assess (Adequate / Missing points / Incorrect).

If no SAFETY comment exists, flag it:

**Missing SAFETY comment!**

Every unsafe block should document WHY it's safe:
```rust
// SAFETY:
// - ptr is guaranteed non-null by [reason]
// - alignment is guaranteed by [reason]
// - exclusive access is guaranteed by [reason]
unsafe { ... }
```

### SAFER ALTERNATIVES

| Current Approach | Safer Alternative |
|------------------|-------------------|
| Raw pointer | `NonNull<T>` (null-free guarantee) |
| Manual drop | `ManuallyDrop<T>` |
| Uninitialized memory | `MaybeUninit<T>` |
| `transmute` | `from_ne_bytes` / `to_ne_bytes` |
| Mutable static | `OnceLock` / `Mutex<T>` |
| `mem::zeroed` | `MaybeUninit::zeroed()` |

### VERDICT

| Aspect | Assessment |
|--------|------------|
| **Correctness** | [Correct / Has bugs / Uncertain] |
| **Documentation** | [Well documented / Needs work / Missing] |
| **Can be made safe?** | [Yes - with wrapper / Partially / No] |
| **Recommendation** | [Ship / Refactor / Do not use] |
</output_format>

<unsafe_code_checklist>

Before shipping unsafe code, verify:

- [ ] All invariants are documented in SAFETY comment
- [ ] Invariants are either checked or guaranteed by construction
- [ ] No aliasing of mutable references
- [ ] Pointers are non-null, aligned, and valid
- [ ] Types are correctly initialized before use
- [ ] No data races possible
- [ ] Tested with Miri (`cargo +nightly miri test`)
- [ ] Reviewed by someone familiar with unsafe Rust
</unsafe_code_checklist>

<rules>
- ALWAYS list the invariants - they're the core of unsafe reasoning
- Be paranoid - assume inputs could be anything
- Suggest `NonNull`, `MaybeUninit`, etc. over raw patterns
- Check for SAFETY comments - their absence is a red flag
- Recommend Miri testing for complex unsafe code
</rules>

<success_criteria>
- All unsafe operations identified with their corresponding superpower
- Every invariant listed with verification status (Yes/No/Unclear)
- UB risks analyzed with severity and mitigation for each
- Safe abstraction suggested where possible, with concrete wrapper code
- SAFETY comment audited or flagged as missing
- Safer alternatives table populated with applicable replacements
- Final verdict provided with clear recommendation
</success_criteria>

</unsafe-audit-skill>
