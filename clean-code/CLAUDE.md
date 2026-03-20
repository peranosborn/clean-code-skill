# clean-code/ — Skill Directory

This directory contains the skill definition and its eval suite.

## SKILL.md Structure

- **Frontmatter** (YAML between `---` fences): `name`, `description`. The `description` field is critical — it determines when Claude auto-triggers the skill. Include all relevant trigger words and scenarios.
- **Body** (markdown): The actual rules Claude follows. Organized into four pillars with rules, quick tests, and examples under each.
- **Self-Review Checklist**: A table at the end that Claude runs through before presenting code.

## Editing Rules

- Do not remove the frontmatter — without it, the skill won't load.
- Keep examples concrete and minimal (bad → good pattern).
- Each rule should have a **bold name** followed by a one-paragraph explanation.
- Quick Tests are single-sentence litmus tests in blockquotes.
- When adding a new rule, also add it to the Self-Review Checklist table.

## Eval Schema (evals/evals.json)

```json
{
  "skill_name": "clean-code",
  "evals": [
    {
      "id": <number>,
      "prompt": "<the user prompt to test>",
      "expected_output": "<what a correct response should contain>",
      "files": []
    }
  ]
}
```

- `id`: sequential integer
- `prompt`: the exact user message to send to Claude
- `expected_output`: human-readable description of what the response should demonstrate
- `files`: optional list of file paths to include as context (empty for most clean-code evals)

When adding a new eval, increment the id and ensure the expected_output references specific clean code principles by name.
