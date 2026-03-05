<explain-skill>

<task>
Explain the function signature at $ARGUMENTS so the user deeply understands each component.

Break down: generics, trait bounds, lifetimes, parameters, return types. Use the explanation style that maps each piece visually and explains WHY it exists, not just WHAT it is.
</task>

<process>
1. **Locate the function** from $ARGUMENTS (file:line format or search by name)

2. **Identify the module context**:
   - Determine the module path (e.g., `crate::domain::email`)
   - Note the file location (e.g., `src/domain/email.rs`)
   - If it's in an `impl` block, note the type being implemented

3. **Extract the full signature** (may span multiple lines with `where` clauses)

4. **Break down each component**:
   - **Function name and visibility** (`pub`, `async`, etc.)
   - **Generic parameters** (`<T, U>`) - what are they placeholders for?
   - **Trait bounds** (`where T: Trait`) - what capabilities are required and WHY?
   - **Lifetimes** (`'a`, `'static`) - what do they constrain?
   - **Parameters** - types, references, mutability
   - **Return type** - what comes back, any wrapping (Result, Option)?

5. **Provide a visual breakdown** like:
   ```
   pub async fn example<T>(&self, input: T) -> Result<Output, Error>
   //  ^^^^^  ^^^^^^^  ^^^       ^^^^^^^     ^^^^^^^^^^^^^^^^^^^^^^^^
   //  |      |        |         |           return type with error handling
   //  |      |        |         parameter using generic T
   //  |      |        generic type parameter
   //  |      async function
   //  public visibility
   ```

6. **Explain the mental model**: what question does each part answer?

7. **If there's a simpler alternative syntax** (e.g., `impl Trait`), mention it

8. **Provide 2-3 usage examples** showing how to call the function:
   - Basic/happy path call
   - With different argument types (if generic)
   - Common patterns from the codebase (search for existing usages)
   - Show `.await` for async, error handling for Result, etc.
</process>

<output_format>

## MODULE CONTEXT

```
File:   src/domain/email.rs
Module: crate::domain::email
Type:   impl Email  (if applicable)
```

**To use this function from another module:**
```rust
use crate::domain::email::Email;
// Then call: Email::parse(...)

// Or for free functions:
use crate::utils::auth::generate_auth_cookie;
```

## FUNCTION SIGNATURE BREAKDOWN

```rust
pub fn example(...) -> ReturnType
// Visual annotation here
```

## COMPONENT ANALYSIS

### `pub` — Visibility

```rust
pub fn example(...)
// ───
//  │
//  └── Callable from outside module `crate::path::to::module`
```

**Why public?** [Explanation of why this visibility level]

**Who calls this?** [List key callers from codebase if known]

[Continue with other components...]

## USAGE EXAMPLES

### 1. Basic Call
```rust
use crate::module::Type;
Type::method(args)
```

[Additional examples...]

</output_format>

<success_criteria>
- Module path and file location clearly identified
- Example `use` statement provided showing how to import
- User can read the signature left-to-right and understand each piece
- WHY each constraint exists is explained, not just what it is
- Visual breakdown provided for complex signatures
- Mental model given ("generics = for any type that...")
- Simpler alternatives mentioned if applicable
- 2-3 concrete usage examples provided with proper imports
</success_criteria>

</explain-skill>
