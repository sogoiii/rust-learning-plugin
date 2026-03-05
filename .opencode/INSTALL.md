# Installing Rust Learning for OpenCode

## Prerequisites

- [OpenCode.ai](https://opencode.ai) installed
- Git

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/sogoiii/rust-learning-plugin.git ~/.config/opencode/rust-learning
```

### 2. Register the plugin

Create a symlink so OpenCode discovers the plugin:

```bash
mkdir -p ~/.config/opencode/plugins
rm -f ~/.config/opencode/plugins/rust-learning.js
ln -s ~/.config/opencode/rust-learning/.opencode/plugins/rust-learning.js ~/.config/opencode/plugins/rust-learning.js
```

### 3. Symlink skills

Create a symlink so OpenCode's native skill tool discovers the skills:

```bash
mkdir -p ~/.config/opencode/skills
rm -rf ~/.config/opencode/skills/rust-learning
ln -s ~/.config/opencode/rust-learning/skills/rust-learning ~/.config/opencode/skills/rust-learning
```

### 4. Symlink agents

Create a symlink so OpenCode discovers the teacher routing agent:

```bash
mkdir -p ~/.config/opencode/agents
rm -f ~/.config/opencode/agents/teacher.md
ln -s ~/.config/opencode/rust-learning/.opencode/agents/teacher.md ~/.config/opencode/agents/teacher.md
```

### 5. Restart OpenCode

Restart OpenCode. The plugin will automatically inject context.

Verify by asking: "what rust-learning skills are available?"

## Usage

### Loading a skill

Use OpenCode's native `skill` tool:

```
use skill tool to load rust-learning
```

### Available workflows

error, trace-borrow, ownership-flow, why-clone, trace-lifetimes, trait-bounds, smart-pointer, compare-dispatch, explain, mental-model, variations, trace-async, expand-macro, pattern-match, unsafe-audit, test-scaffold, assess-finding.

### Tool mapping

When skills reference Claude Code tools:
- `TodoWrite` → `update_plan`
- `Task` with subagents → `@mention` syntax
- `Skill` tool → OpenCode's native `skill` tool
- `Read`, `Write`, `Edit`, `Bash` → Your native tools

## Updating

```bash
cd ~/.config/opencode/rust-learning && git pull
```

## Uninstalling

```bash
rm ~/.config/opencode/plugins/rust-learning.js
rm -rf ~/.config/opencode/skills/rust-learning
rm -f ~/.config/opencode/agents/teacher.md
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

### Teacher agent not found

1. Check agent symlink: `ls -l ~/.config/opencode/agents/teacher.md`
2. Verify it points to: `~/.config/opencode/rust-learning/.opencode/agents/teacher.md`
3. Run `opencode agent list` to see discovered agents

## Getting Help

- Report issues: https://github.com/sogoiii/rust-learning-plugin/issues
- Full documentation: https://github.com/sogoiii/rust-learning-plugin/blob/main/docs/README.opencode.md
