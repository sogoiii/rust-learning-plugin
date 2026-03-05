<async-trace-skill>

<task>
Trace the execution flow of async code, showing WHERE execution can yield, WHAT state machine is created, and HOW the runtime drives it.
</task>

<input>

```rust
$ARGUMENTS
```
</input>

<mental_model>

**The Cooperative Kitchen**

- **Async Runtime** = Head chef (single-threaded) or kitchen staff (multi-threaded)
- **`async fn`** = Recipe card (doesn't cook anything by itself)
- **`Future`** = The recipe in progress (can be paused/resumed)
- **`.await`** = "Chef, I'm waiting for X. Go help someone else until it's ready."
- **Polling** = Chef checking "Is this ready yet?"
</mental_model>

<context_gathering>

**MANDATORY — do this BEFORE tracing async flow**

1. **Locate the code** — If $ARGUMENTS is a file:line or function name, use Read/Grep/Glob to find the actual code. If it's a code snippet, use it directly.

2. **Identify the async runtime** — Grep for `#[tokio::main]`, `#[async_std::main]`, or `smol` in the project to determine which runtime drives the futures.

3. **Find .await chains** — Read the full async function and grep for `.await` to map all yield points and nested async calls.

4. **Read called async functions** — For each `.await`ed call, read its signature to understand if it's I/O-bound, CPU-bound, or spawning tasks.

5. **Check for blocking calls** — Grep the function for `std::thread::sleep`, `std::fs::`, `std::net::`, or other sync I/O that would block the executor.

Only proceed after understanding the runtime, the full await chain, and any blocking hazards.
</context_gathering>

<process>

1. **Identify Async Boundaries**
   - `async fn` declarations
   - `async {}` blocks
   - `.await` points

2. **Map the State Machine**
   Every `async fn` becomes a state machine with states at each `.await`:
   ```
   async fn example() {      // State machine created
       step_a();             // State 0: Before first await
       fut_b().await;        // ─── YIELD POINT ───
       step_c();             // State 1: After first await
       fut_d().await;        // ─── YIELD POINT ───
       step_e();             // State 2: After second await
   }                         // State 3: Complete
   ```

3. **Trace Execution Flow**
   ```
   CALL example() ──────────────────────────────────┐
                                                    │
   ┌────────────────────────────────────────────────┴──┐
   │ Returns Future<Output=()>  (NOT executed yet!)    │
   └────────────────────────────────────────────────┬──┘
                                                    │
   .await or spawn() ───────────────────────────────┤
                                                    │
   ┌────────────────────────────────────────────────┴──┐
   │ Runtime POLLS the future                          │
   │   → Runs until .await                             │
   │   → Returns Pending or Ready                      │
   └───────────────────────────────────────────────────┘
   ```

4. **Identify Blocking Hazards**
   - Sync code in async context (CPU-bound work)
   - Blocking I/O (std::fs, std::net)
   - Long computations without yield points

5. **Check Runtime Requirements**
   - Is an async runtime configured?
   - Single-threaded vs multi-threaded
   - Which runtime (Tokio, async-std, smol)?
</process>

<output_format>

### ASYNC STRUCTURE

```
[Code with await points marked]
```

| Line | Type | Description |
|------|------|-------------|
| X | async fn | Creates Future |
| Y | .await | Yield point |

### STATE MACHINE DIAGRAM

```
┌─────────────┐     poll()     ┌─────────────┐
│   State 0   │ ─────────────► │   State 1   │
│ (before a1) │    Pending     │ (after a1)  │
└─────────────┘                └─────────────┘
                                     │
                               poll()│Pending
                                     ▼
                               ┌─────────────┐
                               │   State 2   │
                               │ (after a2)  │
                               └─────────────┘
                                     │
                               poll()│Ready(value)
                                     ▼
                               ┌─────────────┐
                               │  Complete   │
                               └─────────────┘
```

### EXECUTION TRACE

```
TIME │ ACTION                          │ STATE
─────┼─────────────────────────────────┼─────────
  1  │ example() called                │ Future created (not running)
  2  │ future.await / spawn            │ Runtime takes over
  3  │ Runtime polls future            │ State 0 → runs step_a()
  4  │ Hits fut_b().await              │ Returns Pending, yields
  5  │ Runtime does other work...      │ (future suspended)
  6  │ fut_b ready, runtime re-polls   │ State 1 → runs step_c()
  ...
```

### BLOCKING HAZARDS

| Line | Issue | Fix |
|------|-------|-----|
| [line] | [blocking call] | [use async alternative or spawn_blocking] |

### RUNTIME CHECK

- **Runtime needed:** Yes/No
- **Detected:** `#[tokio::main]` / `#[async_std::main]` / None
- **Thread model:** Single-threaded / Multi-threaded

If no runtime is detected, include this warning:
> **Warning:** No async runtime detected. Add `#[tokio::main]` or similar.

### COMMON PITFALLS IN THIS CODE

1. [Pitfall and fix]
2. [Pitfall and fix]
</output_format>

<patterns_reference>

### Pattern: Sequential Awaits
```rust
let a = fetch_a().await;  // Waits for a
let b = fetch_b().await;  // Then waits for b (sequential!)
```

### Pattern: Concurrent Awaits
```rust
let (a, b) = tokio::join!(fetch_a(), fetch_b());  // Concurrent!
// or
let (a, b) = futures::future::join(fetch_a(), fetch_b()).await;
```

### Pattern: Select (First to Complete)
```rust
tokio::select! {
    val = future_a() => { /* a finished first */ }
    val = future_b() => { /* b finished first */ }
}
```

### Pattern: Spawn (Background Task)
```rust
let handle = tokio::spawn(async { /* runs independently */ });
// ... do other work ...
let result = handle.await;  // Get result when needed
```
</patterns_reference>

<blocking_code_fixes>

| Blocking | Async Alternative |
|----------|-------------------|
| `std::thread::sleep` | `tokio::time::sleep` |
| `std::fs::read` | `tokio::fs::read` |
| `std::net::TcpStream` | `tokio::net::TcpStream` |
| CPU-heavy work | `tokio::task::spawn_blocking` |
</blocking_code_fixes>

<rules>
- ALWAYS identify yield points (.await)
- Show the state machine mental model
- Warn about blocking in async context
- Check for runtime configuration
- Suggest join!/select! for concurrent operations
</rules>

<success_criteria>
- All .await yield points identified and marked
- State machine diagram accurately reflects async fn structure
- Execution trace shows correct polling/suspend/resume flow
- Blocking hazards flagged with specific async alternatives
- Runtime configuration checked and warnings issued if missing
- Concurrent operation opportunities identified (join!/select!)
</success_criteria>

</async-trace-skill>
