# Rust Learning for Codex

Guide for using Rust Learning skills with OpenAI Codex via native skill discovery.

## Quick Install

Tell Codex:

```
Fetch and follow instructions from https://raw.githubusercontent.com/sogoiii/rust-learning-plugin/refs/heads/main/.codex/INSTALL.md
```

## Manual Installation

### Prerequisites

- OpenAI Codex CLI
- Git

### Steps

1. Clone the repo:
   ```bash
   git clone https://github.com/sogoiii/rust-learning-plugin.git ~/.codex/rust-learning
   ```

2. Create the skills symlink:
   ```bash
   mkdir -p ~/.agents/skills
   ln -s ~/.codex/rust-learning/skills/rust-learning ~/.agents/skills/rust-learning
   ```

3. Restart Codex.

### Windows

Use a junction instead of a symlink:

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.agents\skills"
cmd /c mklink /J "$env:USERPROFILE\.agents\skills\rust-learning" "$env:USERPROFILE\.codex\rust-learning\skills\rust-learning"
```

## How It Works

Codex has native skill discovery — it scans `~/.agents/skills/` at startup, parses SKILL.md frontmatter, and loads skills on demand. Rust learning skills are made visible through a single symlink:

```
~/.agents/skills/rust-learning/ → ~/.codex/rust-learning/skills/rust-learning/
```

## Usage

Skills are discovered automatically. Codex activates them when:
- You mention a skill by name (e.g., "use rust-learning for trace-borrow")
- The task matches the skill's description
- You explicitly request a Rust explanation

### Available Workflows

| Workflow | What it does |
|----------|-------------|
| error | Decode compiler errors with plain English + fix patterns |
| trace-borrow | Borrow timeline with conflict zones |
| ownership-flow | Move/borrow/clone diagrams with drop analysis |
| why-clone | Audit `.clone()` calls — necessary vs avoidable |
| trace-lifetimes | Scope diagrams for lifetime annotations |
| trait-bounds | Decode complex `where` clauses |
| smart-pointer | Decision tree: Box vs Rc vs Arc vs RefCell vs Mutex |
| compare-dispatch | Static vs dynamic dispatch tradeoffs |
| explain | Break down function signatures |
| mental-model | Build correct mental models for Rust constructs |
| variations | 3-5 idiomatic ways to write the same code |
| trace-async | State machine diagrams, await points |
| expand-macro | Step-by-step macro expansion |
| pattern-match | Exhaustiveness checks, binding modes |
| unsafe-audit | Audit unsafe blocks for invariants and UB |
| test-scaffold | Generate `todo!()` test stubs |
| assess-finding | Evaluate code suggestions with severity |

## Updating

```bash
cd ~/.codex/rust-learning && git pull
```

Skills update instantly through the symlink.

## Uninstalling

```bash
rm ~/.agents/skills/rust-learning
```

**Windows (PowerShell):**
```powershell
Remove-Item "$env:USERPROFILE\.agents\skills\rust-learning"
```

Optionally delete the clone: `rm -rf ~/.codex/rust-learning` (Windows: `Remove-Item -Recurse -Force "$env:USERPROFILE\.codex\rust-learning"`).

## Troubleshooting

### Skills not showing up

1. Verify the symlink: `ls -la ~/.agents/skills/rust-learning`
2. Check skills exist: `ls ~/.codex/rust-learning/skills/rust-learning`
3. Restart Codex — skills are discovered at startup

### Windows junction issues

Junctions normally work without special permissions. If creation fails, try running PowerShell as administrator.

## Getting Help

- Report issues: https://github.com/sogoiii/rust-learning-plugin/issues
- Main documentation: https://github.com/sogoiii/rust-learning-plugin
