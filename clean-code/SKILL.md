---
name: clean-code
description: >
  Enforces clean code best practices during code generation and code review, based on principles
  based on Robert C. Martin's Clean Code philosophy. Covers meaningful naming, function design, commenting discipline,
  and formatting standards. Use this skill whenever you are writing code, generating code,
  reviewing code, refactoring, doing a code review, checking code quality, or when the user
  mentions "clean code", "code quality", "code review", "naming conventions", "refactor",
  "readable code", "code smell", or "best practices". Also trigger when generating any
  non-trivial code (functions, components, modules) to ensure output follows clean code
  principles from the start — don't wait to be asked.
---

# Clean Code — Generation & Review Guidelines

These principles are rooted in Robert C. Martin's *Clean Code* philosophy. Apply them every time you write or review code.

The core philosophy: **it's okay if your first draft is messy — just don't leave it that way.** Write it, then refine it. Clean code is rewritten code.

---

## When Generating Code

Before writing any function, component, or module, mentally run through these four lenses — naming, function design, commenting, and formatting. If the code you're about to produce would fail any of the checks below, fix it before presenting it to the user.

When the output is more than a trivial snippet, do a self-review pass against these rules before responding. If you spot violations, fix them silently rather than explaining that you almost wrote bad code.

## When Reviewing Code

Walk through the user's code against each section below. Flag specific violations with the rule name (e.g. "**Names: Don't Lie** — `allUsers` is actually a filtered subset"). Suggest concrete fixes, not vague advice. Prioritise the issues that most harm readability.

---

## 1. Meaningful Names

Code is read far more often than it is written. Every name — variable, function, parameter, component — is a micro-explanation. If the reader has to look elsewhere to understand what something means, the name has failed.

### Rules

**Reveal intent.** A name should say why it exists, what it does, and how it's used — without needing a comment. If you have to check the implementation to understand the name, the name failed.

**Don't lie.** If a name promises something it doesn't deliver — like calling a filtered subset `allItems` — it erodes trust and causes bugs. Trust in code starts with honest naming.

**Read like human language.** Code is for people. `isEligibleForDiscount` reads like English; `flag2` doesn't. Prefer names that form natural phrases when used in conditions and assignments.

**Make names searchable.** Single-letter variables and magic numbers are impossible to grep for. `MAX_RETRY_COUNT` is findable; `3` buried in a loop condition is not.

**Skip type encoding.** Don't prefix names with types (`strName`, `arrItems`, `iCount`). Modern languages and tooling handle types — let them. Focus names on meaning, not implementation.

**Tell a story.** Every function name should lead the reader naturally to the next. A well-named call chain reads like a narrative: `validateOrder → calculateTotal → applyDiscount → submitPayment`.

### Quick Test

> "Could a new team member understand this variable without reading its definition?"

If the answer is no, rename it.

### Example

```
// Bad — unclear, unsearchable, dishonest
const d = getItems();
const calc = (w, p) => w * 1000 * (p / 100);
const allUsers = users.filter(u => u.isActive); // lies

// Good — intent-revealing, searchable, honest
const activeUsers = getActiveUsersForAccount();
const calculateDailyGrams = (weightKg, dailyPct) => weightKg * 1000 * (dailyPct / 100);
const activeUsers = users.filter(user => user.isActive);
```

---

## 2. Functions

Every function should be transparently obvious and tell a story that leads the reader naturally to the next function. Massive functions bury business logic under low-level details — extracting them makes your code document itself.

### Rules

**Do one thing.** A function should either DO something (a command) or ANSWER something (a query), but not both. This is command-query separation. If you can't describe the function without using "and", it does too much.

**Keep arguments low.** Arguments add mental weight. Ideal: zero or one. Acceptable: two. Three needs strong justification. More than three: group into an options object and destructure. Each argument is a concept the reader must hold in their head.

**One level of abstraction.** Mixing high-level orchestration and low-level detail is always confusing and obscures intent. A function that calls `validateInput()`, then manually iterates bytes, then calls `saveToDatabase()` is jumping between floors. Keep each function on one floor.

**Bury the switch.** Switch statements and long conditional chains belong behind polymorphism or well-named helper functions. Keep your high-level logic clean — the reader should see the *what*, not the branching mechanics.

**No side effects.** A side effect is when a function modifies state outside its own scope or interacts with the outside world unexpectedly. A function called `checkPassword` that also initialises a session is lying about what it does. Pure functions (same input → same output, no side effects) are trivially testable and safe to call anywhere.

**Use exceptions, not error codes.** Error codes force every caller to check immediately, cluttering the happy path. Exceptions let you separate error handling from business logic. Use try/catch and handle errors at the right level.

**DRY — don't repeat yourself.** Every piece of knowledge should have one authoritative home. Duplicated logic means duplicated bugs — when you fix one copy, you must find every other. You won't.

**Refine the first draft.** Nobody writes clean code on the first pass. Write it messy if you must, then extract, rename, and reshape until each function reads like a single clear thought.

### Quick Test

> "Can I name what this function does in a single short phrase — with no 'and'?"

If not, split it.

### Example

```
// Bad — does two things, mixed abstraction levels
function processOrderAndSendEmail(order) {
  const tax = order.items.reduce((sum, i) => sum + i.price * 0.2, 0);
  const total = order.items.reduce((sum, i) => sum + i.price, 0) + tax;
  // ...30 more lines of SMTP setup and HTML templating
}

// Good — one thing per function, reads like a story
function processOrder(order) {
  const total = calculateTotal(order);
  await chargePayment(order.paymentMethod, total);
  await sendConfirmation(order.email, total);
}
```

---

## 3. Comments

The best comment is the one you didn't need to write because the code explains itself. Stop commenting *what* your code does — rename it, extract it, and let it speak for itself.

### Rules

**Code tells you how; comments tell you why.** Good comments explain intent, warn of consequences, or clarify constraints that aren't obvious from the code. If the code already says it, the comment is noise. Delete it.

**Comments rot.** Code evolves, comments may not. A stale comment is worse than no comment — it actively misleads. If you must comment, keep it glued to the code it describes and update both together, or delete the comment.

**Warning comments are good.** Comments that protect code from well-meaning mistakes are valuable — things like "Do not reorder these calls; X depends on Y being initialised first" or "This looks wrong but is intentional because [reason]".

**Don't apologise.** When a comment apologises for structure (`// sorry, this is a mess` or `// TODO: refactor this`), the structure needs to change, not a comment. An apology comment is a code smell masquerading as self-awareness.

**Let git handle history.** Commented-out code, attribution comments (`// Added by John, March 2024`), inline changelogs — git tracks all of this better than comments ever could. Delete dead code. Your version control is your changelog.

**Rename rather than comment.** If you're about to write a comment explaining what a block does, extract that block into a function with a descriptive name instead. The name *is* the comment, and it can't go stale.

**Clarify, don't confuse.** If your team can't read a comment instantly and understand it, rewrite the comment. Comments should communicate, never confuse. Ambiguous comments are worse than no comments.

**Docs are not comments.** Inline comments are for maintainers. Public API documentation (JSDoc, docstrings) is for consumers. They serve different audiences — don't confuse the two.

### Quick Test

> "Could I delete this comment and still understand the code?"

If yes, delete it.

### Example

```
// Bad — restates the obvious
// Calculate the total price
const totalPrice = items.reduce((sum, item) => sum + item.price, 0);

// Good — explains WHY
// Round after summing to avoid cumulative floating-point drift across line items
const totalPrice = Math.round(items.reduce((sum, item) => sum + item.price, 0) * 100) / 100;

// Good — warns about a non-obvious constraint
// This timeout must exceed the P99 latency of the upstream service (currently ~4.2s).
// Setting it lower causes spurious failures during deployments.
const REQUEST_TIMEOUT_MS = 6000;
```

---

## 4. Formatting

The simplest formatting rules do the most heavy lifting. Consistent formatting makes code scannable — the reader's eyes follow structure without needing to read every line.

### Rules

**Newspaper order.** Highest-level function at the top, every definition below its caller. Read a file top-to-bottom like a newspaper article — headline first, supporting detail below. The reader should never have to scroll up to find what a function calls.

**Declare close to usage.** Variables should appear near where they're used. Don't declare everything at the top of a function and then use it 40 lines later. Proximity signals relationship.

**Team rules win.** Consistency matters more than any individual preference. The team picks a style, then everyone follows it everywhere. Enforce automatically with formatters (Prettier) and linters (ESLint) so nobody has to think about it.

**Vertical space signals separation.** Use blank lines to separate *concepts*, not individual statements. Related lines should sit close together. Unrelated blocks should have breathing room. The whitespace is a visual guide to the code's logical structure.

**Group related properties.** Keep class fields, component props, or configuration values grouped in one place. Don't scatter related declarations across a file — grouping is a form of documentation.

**Keep files focused.** A file should have one clear reason to exist. If a file has 500+ lines, it's probably doing too many things. Split by responsibility, not by arbitrary line count.

### Quick Test

> "Can I scan this file and understand its structure without reading the code?"

If the formatting doesn't reveal the shape of the logic, restructure.

---

## Self-Review Checklist

Before presenting generated code or finishing a code review, run through this:

| Area | Check |
|------|-------|
| Names | Every variable and function name reveals intent without needing context? |
| Names | No name lies about what it contains or does? |
| Names | All names are searchable (no single-letter vars outside tiny loops)? |
| Functions | Every function does exactly one thing? |
| Functions | No function has more than 3 parameters? |
| Functions | No function mixes abstraction levels? |
| Functions | No hidden side effects? |
| Comments | No comment restates what the code already says? |
| Comments | Every comment explains *why*, not *what*? |
| Comments | No commented-out code left behind? |
| Formatting | Top-level functions appear before their helpers? |
| Formatting | Variables declared near where they're used? |
| Formatting | Blank lines separate concepts, not random lines? |

---

