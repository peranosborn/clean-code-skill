# Clean Code Skill

A Claude skill that enforces clean code best practices during code generation and code review.

## Project Structure

```
clean-code-skill/
├── CLAUDE.md                    # This file — project-level guidance
├── README.md                    # Public-facing docs
├── LICENSE                      # MIT
├── clean-code-cheatsheet.html   # Printable dark-themed cheat sheet
└── clean-code/
    ├── CLAUDE.md                # Skill-specific editing guidance
    ├── SKILL.md                 # The skill definition (frontmatter + rules)
    └── evals/
        └── evals.json           # Eval test cases (3 scenarios)
```

## Four Pillars

1. **Meaningful Names** — intent-revealing, searchable, honest
2. **Functions** — single responsibility, low arguments, no side effects, CQS
3. **Comments** — explain why not what, delete noise, let git handle history
4. **Formatting** — newspaper order, declare close to usage, vertical space as structure

## Editing Guidelines

- **SKILL.md is the core deliverable.** All changes to the skill's behavior go there.
- The frontmatter `description` field in SKILL.md controls when Claude triggers the skill — keep it comprehensive.
- The cheat sheet (`clean-code-cheatsheet.html`) is a standalone HTML file; regenerate it if SKILL.md rules change.
- Evals in `evals/evals.json` validate that the skill produces the right behavior. Add new test cases when adding new rules.

## Benchmark Results

| Metric | With Skill | Without Skill | Delta |
|--------|-----------|--------------|-------|
| Pass Rate | 94.7% | 68.4% | +26.3% |
| Code Review | 100% | 57.1% | +42.9% |
| Refactoring | 100% | 85.7% | +14.3% |

## Common Tasks

- **Add a new rule:** Add it under the appropriate pillar in SKILL.md, add a row to the self-review checklist, and add an eval case to evals.json.
- **Update trigger conditions:** Edit the `description` field in SKILL.md frontmatter.
- **Update the cheat sheet:** Edit clean-code-cheatsheet.html to match any SKILL.md changes.
