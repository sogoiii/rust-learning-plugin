<smart-pointer-skill>

<task>
Help choose the right smart pointer for the user's situation using a decision tree approach.
</task>

<input>
**Problem:** $ARGUMENTS
</input>

<mental_model>
## Smart Pointers as Access Patterns

| Pointer | Who Owns? | How Many? | Thread Safe? | Mutability |
|---------|-----------|-----------|--------------|------------|
| `Box<T>` | Single owner | 1 | Yes | Via owner |
| `Rc<T>` | Shared | N (single thread) | No | Read-only |
| `Arc<T>` | Shared | N (multi thread) | Yes | Read-only |
| `RefCell<T>` | Single owner | 1 | No | Interior mut |
| `Mutex<T>` | Shared | N (multi thread) | Yes | Interior mut |
| `RwLock<T>` | Shared | N (multi thread) | Yes | Interior mut (read/write) |
</mental_model>

<context_gathering>

**MANDATORY — do this BEFORE recommending a smart pointer**

1. **Locate the code** — If $ARGUMENTS is a file:line or function name, use Read/Grep/Glob to find the actual code. If it's a code snippet or problem description, use it directly.

2. **Search for existing smart pointer usage** — Grep the codebase for `Box<`, `Rc<`, `Arc<`, `RefCell<`, `Mutex<`, `RwLock<` to understand the project's existing patterns and preferences.

3. **Check threading model** — Grep for `tokio::spawn`, `thread::spawn`, `async fn`, `Send`, `Sync` to determine if multi-threading is in play.

4. **Read the ownership context** — Read the struct/module where the pointer will be used to understand how many owners need access and mutation requirements.

5. **Check existing type definitions** — Read the type that will be wrapped to assess its size, Clone cost, and whether it implements Send/Sync.

Only proceed after understanding the project's threading model and existing pointer conventions.
</context_gathering>

<decision_tree>
```
Do you need MULTIPLE OWNERS of the same data?
│
├─ NO → Is the data recursive or unsized?
│       │
│       ├─ YES → Box<T>
│       │        (heap allocation, single owner)
│       │
│       └─ NO → Do you need interior mutability?
│               │
│               ├─ YES → RefCell<T>
│               │        (runtime borrow checking)
│               │
│               └─ NO → Just use owned T or &T
│
└─ YES → Is this multi-threaded?
         │
         ├─ NO → Do you need to mutate the shared data?
         │       │
         │       ├─ YES → Rc<RefCell<T>>
         │       │        (shared ownership + interior mutability)
         │       │
         │       └─ NO → Rc<T>
         │               (shared ownership, read-only)
         │
         └─ YES → Do you need to mutate the shared data?
                  │
                  ├─ YES → Arc<Mutex<T>> or Arc<RwLock<T>>
                  │        (thread-safe shared ownership + mutation)
                  │
                  └─ NO → Arc<T>
                          (thread-safe shared ownership, read-only)
```
</decision_tree>

<reference>
### Box\<T\> - Heap Allocation
```rust
// When: recursive types, large data, trait objects
let boxed = Box::new(5);
let large = Box::new([0u8; 1_000_000]);  // Avoid stack overflow
let dyn_obj: Box<dyn Trait> = Box::new(concrete);
```
**Use for:** Recursive types (`Box<Node>`), large data, unsized types.

### Rc\<T\> - Reference Counted (Single Thread)
```rust
// When: multiple owners, single thread
use std::rc::Rc;

let a = Rc::new(String::from("shared"));
let b = Rc::clone(&a);  // Cheap! Just increments counter
let c = Rc::clone(&a);
// a, b, c all point to same String
// Dropped when last Rc goes out of scope
```
**Use for:** Shared graphs, trees with multiple parents, caches.

### Arc\<T\> - Atomic Reference Counted (Multi Thread)
```rust
// When: multiple owners ACROSS threads
use std::sync::Arc;
use std::thread;

let data = Arc::new(vec![1, 2, 3]);
let data_clone = Arc::clone(&data);

thread::spawn(move || {
    println!("{:?}", data_clone);  // Safe!
});
```
**Use for:** Shared data between threads, thread pools.

### RefCell\<T\> - Interior Mutability (Single Thread)
```rust
// When: need to mutate through &self
use std::cell::RefCell;

struct Cache {
    data: RefCell<HashMap<K, V>>,
}

impl Cache {
    fn get(&self, key: &K) -> V {  // Note: &self, not &mut self
        let mut data = self.data.borrow_mut();  // Runtime check!
        // ... mutate data ...
    }
}
```
**Use for:** Mock objects, caches, observer pattern.

### Mutex\<T\> - Mutual Exclusion (Multi Thread)
```rust
// When: need to mutate shared data across threads
use std::sync::Mutex;

let counter = Arc::new(Mutex::new(0));
let counter_clone = Arc::clone(&counter);

thread::spawn(move || {
    let mut num = counter_clone.lock().unwrap();
    *num += 1;
});
```
**Use for:** Shared mutable state between threads.

### RwLock\<T\> - Read-Write Lock (Multi Thread)
```rust
// When: many readers, occasional writers
use std::sync::RwLock;

let data = Arc::new(RwLock::new(vec![1, 2, 3]));

// Multiple readers OK
let read_guard = data.read().unwrap();

// Single writer (blocks readers)
let mut write_guard = data.write().unwrap();
```
**Use for:** Read-heavy workloads with occasional writes.
</reference>

<common_combinations>
| Pattern | Use Case |
|---------|----------|
| `Rc<RefCell<T>>` | Shared mutable state, single thread |
| `Arc<Mutex<T>>` | Shared mutable state, multi thread |
| `Arc<RwLock<T>>` | Read-heavy shared state, multi thread |
| `Box<dyn Trait>` | Owned trait object |
| `Rc<dyn Trait>` | Shared trait object, single thread |
| `Arc<dyn Trait + Send + Sync>` | Shared trait object, multi thread |
</common_combinations>

<output_format>
### YOUR SITUATION ANALYSIS

| Question | Answer |
|----------|--------|
| Multiple owners? | [Yes/No] |
| Multi-threaded? | [Yes/No] |
| Need mutation? | [Yes/No] |
| Recursive type? | [Yes/No] |

### RECOMMENDATION

**Use: `[SmartPointer<T>]`**

Reason: [Why this is the right choice]

### CODE EXAMPLE

```rust
[Concrete example for their situation]
```

### COMMON PITFALLS

- **Rc + threads**: `Rc` is NOT thread-safe. Use `Arc` for threads.
- **RefCell + threads**: `RefCell` is NOT thread-safe. Use `Mutex` for threads.
- **Mutex deadlock**: Don't hold lock while calling code that might lock again.
- **RefCell panic**: `borrow_mut()` while already borrowed panics at runtime.

### ALTERNATIVES CONSIDERED

| Option | Why Not |
|--------|---------|
| [Alternative 1] | [Reason it doesn't fit] |
| [Alternative 2] | [Reason it doesn't fit] |
</output_format>

<rules>
- Ask clarifying questions if situation is ambiguous
- Always mention thread safety implications
- Warn about common pitfalls for the recommended type
- Show concrete code example for their use case
</rules>

<success_criteria>
- The user's ownership/mutability/threading needs are correctly identified
- The recommended smart pointer matches the decision tree logic
- A concrete code example demonstrates usage for the user's specific problem
- Thread safety implications are explicitly addressed
- Common pitfalls for the recommended type are warned about
</success_criteria>

</smart-pointer-skill>
