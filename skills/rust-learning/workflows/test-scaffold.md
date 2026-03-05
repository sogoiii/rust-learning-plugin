<test-scaffold-skill>

<task>
Generate a test scaffold with `todo!()` stubs for $ARGUMENTS. Produce ONLY function signatures with `todo!()` bodies — no implementation. The scaffold should cover happy paths, error paths, and edge cases based on the actual code.
</objective>

<process>

1. **Locate the code** from $ARGUMENTS:
   - If `file:line` → Read that file at that line
   - If function/trait name → Grep the codebase to find definition
   - If file path → Read the entire file

2. **Read the source** — Get the full implementation plus surrounding context (imports, struct definitions, trait definitions it implements)

3. **Identify the code type**:
   - Single function → scaffold tests for that function
   - Trait impl block → scaffold tests for each method
   - Struct with methods → scaffold tests for each public method
   - Entire module → scaffold tests for all public items

4. **Detect the project's test style** by scanning existing tests:
   - Check for `#[tokio::test]` (async tests)
   - Check for `#[test]` (sync tests)
   - Check for `rstest` usage (`#[rstest]`, `#[fixture]`)
   - Check for `proptest` usage
   - Check for common helper patterns (setup functions, test fixtures)
   - Check naming convention (`should_*`, `test_*`, `it_*`, etc.)
   - Look at existing `#[cfg(test)] mod tests` blocks in nearby files

5. **Analyze each function/method for test cases**:
   - **Return type**: `Result<T, E>` → need Ok and Err cases per variant
   - **Option returns**: need Some and None cases
   - **Match/if branches**: one test per logical branch
   - **Validation logic**: valid input, each invalid input type
   - **Parameters**: boundary values, empty inputs, typical inputs
   - **Side effects**: state changes (add then verify, remove then verify)
   - **Error enum variants**: one test per variant that this function can return

6. **Generate the scaffold** following this structure:
   ```rust
   #[cfg(test)]
   mod tests {
       use super::*;
       // additional imports if needed based on test style

       // Group: [method/function name]
       #[tokio::test]  // or #[test], matching project style
       async fn should_[action]_[condition]() {
           todo!()
       }
   }
   ```

7. **Present the scaffold** — Show only the test module with all stubs. Do NOT write it to any file unless the user asks.
</process>

<test_naming>
Follow the naming convention detected in step 4. If no existing convention, default to:
- `should_[expected_behavior]` for happy paths
- `should_[fail/return_error]_if_[condition]` for error paths
- `should_[handle/reject]_[edge_case]` for edge cases

Group tests with comments by the function/method they test:
```rust
// add_code
async fn should_add_code_successfully() { todo!() }
async fn should_fail_to_add_code_if_duplicate() { todo!() }

// remove_code
async fn should_remove_code_successfully() { todo!() }
```
</test_naming>

<success_criteria>
- Code located and fully read before generating anything
- Test style matches existing project conventions (async vs sync, naming, framework)
- Every public function/method has at least one happy path and one error path test
- Each error variant the code can produce has a corresponding test
- Scaffold uses `todo!()` bodies only — no implementation
- Tests are grouped by function/method with comments
- Output is a single `#[cfg(test)] mod tests` block ready to paste
</success_criteria>

</test-scaffold-skill>
