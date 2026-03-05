# Rust Learning for OpenCode

Guide for using Rust Learning skills with OpenCode.ai.

## Quick Install

Tell OpenCode:

```
Fetch and follow instructions from https://raw.githubusercontent.com/sogoiii/rust-learning-plugin/refs/heads/main/.opencode/INSTALL.md
```

## Manual Installation

### Prerequisites

- [OpenCode.ai](https://opencode.ai) installed
- Git

### macOS / Linux

```bash
# 1. Clone
git clone https://github.com/sogoiii/rust-learning-plugin.git ~/.config/opencode/rust-learning

# 2. Register plugin
mkdir -p ~/.config/opencode/plugins
rm -f ~/.config/opencode/plugins/rust-learning.js
ln -s ~/.config/opencode/rust-learning/.opencode/plugins/rust-learning.js ~/.config/opencode/plugins/rust-learning.js

# 3. Symlink skills
mkdir -p ~/.config/opencode/skills
rm -rf ~/.config/opencode/skills/rust-learning
ln -s ~/.config/opencode/rust-learning/skills/rust-learning ~/.config/opencode/skills/rust-learning

# 4. Restart OpenCode
```

### Windows (PowerShell)

```powershell
# 1. Clone
git clone https://github.com/sogoiii/rust-learning-plugin.git "$env:USERPROFILE\.config\opencode\rust-learning"

# 2. Register plugin
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.config\opencode\plugins"
Remove-Item -ErrorAction SilentlyContinue "$env:USERPROFILE\.config\opencode\plugins\rust-learning.js"
cmd /c mklink /J "$env:USERPROFILE\.config\opencode\plugins\rust-learning.js" "$env:USERPROFILE\.config\opencode\rust-learning\.opencode\plugins\rust-learning.js"

# 3. Symlink skills
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.config\opencode\skills"
Remove-Item -Recurse -ErrorAction SilentlyContinue "$env:USERPROFILE\.config\opencode\skills\rust-learning"
cmd /c mklink /J "$env:USERPROFILE\.config\opencode\skills\rust-learning" "$env:USERPROFILE\.config\opencode\rust-learning\skills\rust-learning"

# 4. Restart OpenCode
```

## How It Works

Rust Learning integrates with OpenCode through two mechanisms:

1. **Plugin** (`.opencode/plugins/rust-learning.js`): Uses OpenCode's `experimental.chat.system.transform` hook to inject skill context into the system prompt at session start.

2. **Skills symlink**: OpenCode's native `skill` tool discovers skills in `~/.config/opencode/skills/`. The symlink exposes the 17 rust-learning workflows.

## Usage

### Loading skills

Use OpenCode's native `skill` tool:

```
use skill tool to load rust-learning
```

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

### Tool Mapping

When skills reference Claude Code tools, substitute OpenCode equivalents:

| Claude Code Tool | OpenCode Equivalent |
|-----------------|-------------------|
| `TodoWrite` | `update_plan` |
| `Task` with subagents | `@mention` syntax |
| `Skill` tool | OpenCode's native `skill` tool |
| `Read`, `Write`, `Edit`, `Bash` | Your native tools |

## Updating

```bash
cd ~/.config/opencode/rust-learning && git pull
```

## Uninstalling

```bash
rm ~/.config/opencode/plugins/rust-learning.js
rm -rf ~/.config/opencode/skills/rust-learning
```

Optionally delete the clone: `rm -rf ~/.config/opencode/rust-learning`.

## Troubleshooting

### Plugin not loading

1. Check plugin symlink: `ls -l ~/.config/opencode/plugins/rust-learning.js`
2. Check source exists: `ls ~/.config/opencode/rust-learning/.opencode/plugins/rust-learning.js`
3. Check OpenCode logs for errors

### Skills not found

1. Check skills symlink: `ls -l ~/.config/opencode/skills/rust-learning`
2. Verify it points to: `~/.config/opencode/rust-learning/skills/rust-learning`
3. Use `skill` tool to list what's discovered

## Getting Help

- Report issues: https://github.com/sogoiii/rust-learning-plugin/issues
- Full documentation: https://github.com/sogoiii/rust-learning-plugin
