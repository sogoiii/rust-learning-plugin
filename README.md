# rust-learning

*Interactive Rust learning tools for Claude Code — understand ownership, borrowing, lifetimes, and more through visual diagrams and guided analysis.*

[![Claude Code](https://img.shields.io/badge/Claude_Code-plugin-blueviolet?style=flat-square)](https://code.claude.com/docs/en/plugins)
[![Skills](https://img.shields.io/badge/skills-17-orange?style=flat-square)](#available-skills)
[![Agent](https://img.shields.io/badge/agent-teacher-green?style=flat-square)](#teacher-agent)

## What is this?

A Claude Code plugin that teaches Rust concepts through ASCII diagrams, annotated code examples, and mental models. It includes **17 interactive skills** and a **dedicated agent** that reads your actual code before explaining anything.

Instead of generic explanations, every response is grounded in your codebase — the agent reads your types, checks ownership patterns, and references specific lines.

## Installation

### Claude Code (Plugin Marketplace)

Register the marketplace, then install:

```bash
/plugin marketplace add sogoiii/rust-learning-plugin
/plugin install rust-learning@rust-learning-tools
```

Or load directly from a local clone:

```bash
git clone https://github.com/sogoiii/rust-learning-plugin.git
claude --plugin-dir ./rust-learning-plugin
```

### Cursor

Within Cursor Agent chat:

```
/plugin-add rust-learning
```

### Codex

Instruct Codex to:

```
Fetch and follow instructions from https://raw.githubusercontent.com/sogoiii/rust-learning-plugin/refs/heads/main/.codex/INSTALL.md
```

### OpenCode

Instruct OpenCode to:

```
Fetch and follow instructions from https://raw.githubusercontent.com/sogoiii/rust-learning-plugin/refs/heads/main/.opencode/INSTALL.md
```

### Verify Installation

Start a fresh session and try a skill:

```
/rust-learning:error <paste a Rust compiler error>
```

> [!NOTE]
> The full plugin experience (teacher agent as default, all 17 slash commands) is available in Claude Code. Other tools support the skills via the [Agent Skills](https://agentskills.io) open standard.

> [!NOTE]
> After installation, the `teacher` agent is activated as the default agent via `settings.json`. This means Claude Code will use the Rust expert persona by default when this plugin is enabled.

## Usage


> [!TIP]
> The **agent** (`teacher.md`) is best for open-ended questions. The **slash commands** are best when you know exactly which analysis you need.


### Teacher Agent

The agent provides conversational Rust explanations. It reads your code first, picks the right skill automatically, and explains with diagrams.

```
@rust-learning:teacher why does this value get moved when I pass it to this function?
@rust-learning:teacher what does this trait bound mean?
```

### Slash Commands (Direct)

Invoke any skill directly with arguments:

```
/rust-learning:error <paste compiler error>
/rust-learning:trace-borrow <paste code snippet>
/rust-learning:smart-pointer I need shared mutable state across async tasks
```

### Interactive Menu

Invoke the skill without a specific workflow to browse all options:

```
/rust-learning
```

## Available Skills

### Compiler Errors

| Skill | Command | What it does |
|-------|---------|-------------|
| **error** | `/rust-learning:error` | Decode compiler errors with plain English + annotated code + fix patterns |

### Ownership & Memory

| Skill | Command | What it does |
|-------|---------|-------------|
| **trace-borrow** | `/rust-learning:trace-borrow` | Borrow timeline with conflict zones and restructuring suggestions |
| **ownership-flow** | `/rust-learning:ownership-flow` | Move/borrow/clone diagrams with drop analysis |
| **why-clone** | `/rust-learning:why-clone` | Audit `.clone()` calls — necessary vs avoidable + alternatives |
| **trace-lifetimes** | `/rust-learning:trace-lifetimes` | Scope diagrams for lifetime annotations + elision rule checks |

### Type System & Generics

| Skill | Command | What it does |
|-------|---------|-------------|
| **trait-bounds** | `/rust-learning:trait-bounds` | Decode complex `where` clauses with visual annotation |
| **smart-pointer** | `/rust-learning:smart-pointer` | Decision tree: Box vs Rc vs Arc vs RefCell vs Mutex |
| **compare-dispatch** | `/rust-learning:compare-dispatch` | Static vs dynamic dispatch tradeoffs with code examples |

### Code Explanation

| Skill | Command | What it does |
|-------|---------|-------------|
| **explain** | `/rust-learning:explain` | Break down function signatures — generics, traits, lifetimes, return types |
| **mental-model** | `/rust-learning:mental-model` | Build correct mental models for any Rust construct |
| **variations** | `/rust-learning:variations` | 3-5 idiomatic ways to write the same code, ranked |

### Async

| Skill | Command | What it does |
|-------|---------|-------------|
| **trace-async** | `/rust-learning:trace-async` | State machine diagrams, await points, blocking hazard detection |

### Macros & Patterns

| Skill | Command | What it does |
|-------|---------|-------------|
| **expand-macro** | `/rust-learning:expand-macro` | Step-by-step macro expansion for `macro_rules!` and derive macros |
| **pattern-match** | `/rust-learning:pattern-match` | Exhaustiveness checks, binding modes, guard analysis |

### Safety & Testing

| Skill | Command | What it does |
|-------|---------|-------------|
| **unsafe-audit** | `/rust-learning:unsafe-audit` | Audit unsafe blocks for invariants, UB risks, safe alternatives |
| **test-scaffold** | `/rust-learning:test-scaffold` | Generate `todo!()` test stubs for functions, traits, or modules |
| **assess-finding** | `/rust-learning:assess-finding` | Evaluate code suggestions/findings with severity + fix options |

## Example Output

For detailed examples of every skill with sample inputs, expected outputs, and thinking flows, see **[WORKFLOW_EXAMPLES.md](WORKFLOW_EXAMPLES.md)**.

Running `/rust-learning:trace-borrow` on a borrow conflict:

```
BORROW TIMELINE
LINE | CODE                      | BORROWS ACTIVE
-----+---------------------------+----------------
  1  | let mut data = vec![..];  | data: OWNER
  2  | let first = &data[0];     | data: OWNER, first: &data
  3  | data.push(4);             | CONFLICT! first still active
  4  | println!("{}", first);    | data: OWNER, first: &data (last use)

BORROW SPAN DIAGRAM
+--- first: &data ----------------------+
|                                       |
----------------------------------------
  2       3 (CONFLICT)       4
          |
          +-- push() needs &mut
```

## Project Structure

```
rust-learning-plugin/
├── .claude-plugin/
│   ├── plugin.json              # Claude Code plugin manifest
│   └── marketplace.json         # Self-hosted marketplace for distribution
├── .cursor-plugin/
│   └── plugin.json              # Cursor plugin manifest
├── .codex/
│   └── INSTALL.md               # Codex installation instructions
├── .opencode/
│   ├── INSTALL.md               # OpenCode installation instructions
│   └── plugins/
│       └── rust-learning.js     # OpenCode plugin (system prompt injection)
├── agents/
│   └── teacher.md               # Conversational Rust expert agent (default)
├── commands/                    # 17 slash command entry points
│   ├── error.md
│   ├── trace-borrow.md
│   └── ...
├── docs/
│   ├── README.codex.md          # Extended Codex documentation
│   └── README.opencode.md       # Extended OpenCode documentation
├── hooks/
│   ├── hooks.json               # SessionStart hook config
│   ├── run-hook.cmd             # Cross-platform hook runner
│   └── session-start            # Injects skill context on startup
├── skills/
│   └── rust-learning/
│       ├── SKILL.md             # Skill router (intake + routing table)
│       ├── references/
│       │   └── borrow-ownership-rules.md
│       └── workflows/           # 17 workflow definitions
│           ├── error.md
│           ├── trace-borrow.md
│           └── ...
├── settings.json                # Default agent configuration
├── WORKFLOW_EXAMPLES.md         # Detailed examples for all 17 skills
└── README.md
```
