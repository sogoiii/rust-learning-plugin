# Installing Rust Learning for Codex

Enable rust-learning skills in Codex via native skill discovery. Clone and symlink.

## Prerequisites

- Git

## Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/sogoiii/rust-learning-plugin.git ~/.codex/rust-learning
   ```

2. **Create the skills symlink:**
   ```bash
   mkdir -p ~/.agents/skills
   ln -s ~/.codex/rust-learning/skills/rust-learning ~/.agents/skills/rust-learning
   ```

   **Windows (PowerShell):**
   ```powershell
   New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.agents\skills"
   cmd /c mklink /J "$env:USERPROFILE\.agents\skills\rust-learning" "$env:USERPROFILE\.codex\rust-learning\skills\rust-learning"
   ```

3. **Restart Codex** to discover the skills.

## Verify

```bash
ls -la ~/.agents/skills/rust-learning
```

You should see a symlink pointing to your rust-learning skills directory.

## Usage

Skills are discovered automatically. Try:

```
use the rust-learning skill for: error <paste a compiler error>
```

Available workflows: error, trace-borrow, ownership-flow, why-clone, trace-lifetimes, trait-bounds, smart-pointer, compare-dispatch, explain, mental-model, variations, trace-async, expand-macro, pattern-match, unsafe-audit, test-scaffold, assess-finding.

## Updating

```bash
cd ~/.codex/rust-learning && git pull
```

Skills update instantly through the symlink.

## Uninstalling

```bash
rm ~/.agents/skills/rust-learning
```

Optionally delete the clone: `rm -rf ~/.codex/rust-learning`.
