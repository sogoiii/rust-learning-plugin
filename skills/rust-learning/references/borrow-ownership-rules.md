<rules>

**The Three Borrow Checker Rules:**

1. **One &mut OR any number of &** — Never both simultaneously
2. **References must be valid** — No dangling references (data outlives all references)
3. **NLL (Non-Lexical Lifetimes)** — Borrows end at LAST USE, not scope end

</rules>

<copy_vs_move>

| Type | Behavior | Why |
|------|----------|-----|
| `i32`, `bool`, `char`, `f64` | Copy | Stack-only, implements `Copy` |
| `&T` (references) | Copy | References are `Copy` |
| Small tuples of Copy types | Copy | All fields are `Copy` |
| `String`, `Vec<T>`, `Box<T>` | Move | Heap data, no `Copy` |
| `HashMap`, `HashSet` | Move | Heap data |
| Structs/enums | Move (default) | Unless `#[derive(Copy, Clone)]` with all `Copy` fields |

</copy_vs_move>

<common_patterns>

**Safe: Sequential Mutations**
```rust
let mut v = vec![1];
v.push(2);  // &mut ends
v.push(3);  // new &mut, OK
```

**Safe: Non-overlapping Borrows (NLL)**
```rust
let mut v = vec![1];
let r = &v[0];
println!("{}", r);  // r's LAST USE — borrow ends here
v.push(2);          // OK! r is no longer active
```

**Unsafe: Overlapping Mutable**
```rust
let mut v = vec![1];
let r = &mut v;
v.push(2);  // ERROR: v already borrowed as &mut
*r = vec![3];
```

**Unsafe: Mixed Mutability**
```rust
let mut v = vec![1];
let r = &v;      // immutable borrow
v.push(2);       // ERROR: needs &mut while & exists
println!("{}", r);
```

</common_patterns>

<ownership_transfer_patterns>

**Move** — Hand the box to someone else
```rust
let a = String::from("x");
let b = a;      // a moved to b, a invalid
```

**Clone** — Make a photocopy
```rust
let a = String::from("x");
let b = a.clone();  // Deep copy, both valid
```

**Borrow** — Let someone look at your box
```rust
let a = String::from("x");
let b = &a;     // Borrow, a still owns
```

**Transfer into Function**
```rust
fn take(s: String) { }  // Takes ownership
let a = String::from("x");
take(a);        // a moved into function, a invalid
```

**Borrow into Function**
```rust
fn look(s: &str) { }  // Borrows
let a = String::from("x");
look(&a);       // Temporary borrow, a still valid
```

</ownership_transfer_patterns>
