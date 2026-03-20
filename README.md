# Clean Code Skill

A Claude skill that enforces clean code best practices during code generation and code review, based on Robert C. Martin's *Clean Code* philosophy.

## What It Does

When installed, this skill automatically applies clean code principles whenever Claude writes or reviews code. It covers four pillars:

- **Meaningful Names** — intent-revealing, searchable, honest naming
- **Functions** — single responsibility, minimal arguments, no side effects, command-query separation
- **Comments** — explain *why* not *what*, delete noise, let git handle history
- **Formatting** — newspaper order, declare close to usage, vertical space as structure

The skill also includes a self-review checklist that Claude runs before presenting generated code.

## Installation

### Claude Code

```bash
claude install-skill /path/to/clean-code-skill/clean-code
```

### Manual

Copy the `clean-code/` directory into your skills folder:

```
~/.claude/skills/clean-code/
└── SKILL.md
```

## Benchmark Results

Tested across 3 scenarios (code generation, code review, React refactoring):

| Metric | With Skill | Without Skill | Delta |
|--------|-----------|--------------|-------|
| Pass Rate | 94.7% | 68.4% | **+26.3%** |
| Code Review | 100% | 57.1% | **+42.9%** |
| Refactoring | 100% | 85.7% | **+14.3%** |

The biggest improvement is in code review, where the skill causes Claude to cite specific clean code principles by name rather than giving generic advice.

## Extras

- `clean-code-cheatsheet.html` — A dark-themed, printable cheat sheet summarising all 32 guidelines across the four pillars. Open in any browser.

## Licence

MIT
