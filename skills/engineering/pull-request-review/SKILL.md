---
name: pull-request-review
description: Review the current branch's changes against a base branch, flagging findings by severity.
disable-model-invocation: true
---

Perform a thorough code review of the current branch's changes against a base branch.

## Setup

Run the following in parallel to gather context:
- `git branch --show-current` — current branch name
- `git log --oneline -10` — recent commits
- `git remote -v` — remote repo info

**Determine the base branch:**
- If the user passed an argument to this command, use that as the base branch.
- Otherwise default to `develop`. If `develop` doesn't exist, fall back to `main`.

## Gather diff data

Run in parallel:
```
git log <base>..HEAD --oneline
git diff <base>...HEAD --stat
git diff <base>...HEAD
```

Also run:
```
gh pr view --json title,body,number,url 2>/dev/null || echo "No open PR found"
```

## Perform the review

Analyze **all changed files** across the diff carefully. For each changed file, consider:

### 1. Correctness
- Logic errors, off-by-one errors, incorrect conditions
- Edge cases that aren't handled
- Incorrect assumptions about data or state

### 2. Python-specific issues
- Mutable default arguments (e.g. `def f(x=[])`)
- Bare `except:` clauses that swallow all exceptions
- Missing `await` on coroutines in async code
- Incorrect use of `is` vs `==` for value comparison
- Variable shadowing builtins or outer scope names
- `__init__` methods that don't call `super().__init__()` when needed
- Type annotation mismatches or missing return types on public APIs
- Unnecessary list comprehensions when a generator suffices

### 3. Security
- Hardcoded secrets, API keys, or credentials
- Command injection risks (e.g. `subprocess` with `shell=True` and user input)
- Unsafe deserialization (`pickle`, `eval`, `exec`)
- Sensitive data logged or exposed in error messages

### 4. Performance
- N+1 query patterns or repeated expensive calls in loops
- Missing `async`/`await` where blocking I/O could be awaited
- Unnecessary deep copies or large data structures created per-request

### 5. Design & maintainability
- Functions or methods doing too many things (single responsibility)
- Duplication that could be extracted into a shared utility
- Magic numbers or strings that should be named constants
- Dead code or commented-out code left behind

### 6. Tests
- Are new code paths covered by tests?
- Are edge cases tested?
- Any tests that only test happy paths without error cases?

### 7. Docs & comments
- Public functions/classes missing docstrings
- Comments that describe *what* instead of *why*
- Outdated comments that no longer reflect the code

## Output format

Present the review in this structure:

---

### PR Review: `<branch>` → `<base>`

**Commits reviewed:** `<N> commits` — `<one-line summary of scope>`

---

#### Summary
A 2–4 sentence high-level assessment of the changes: what they do, overall quality, and any major concerns.

---

#### Findings

For each finding use this format:

> **[SEVERITY] Category — `file.py:line`**
> Brief description of the issue.
> ```python
> # problematic code snippet (if short enough)
> ```
> **Suggestion:** What to do instead.

Severity levels:
- 🔴 **CRITICAL** — must fix before merge (security hole, data loss, crash)
- 🟠 **MAJOR** — significant bug or design flaw, strongly recommend fixing
- 🟡 **MINOR** — small bug, edge case, or code smell worth addressing
- 🔵 **NIT** — style, naming, or minor readability issue

Group findings by file.

---

#### Positives
Call out 2–3 things done well (good abstractions, clear naming, solid test coverage, etc.).

---

#### Verdict

Choose one:
- ✅ **Approve** — ready to merge
- ✅ **Approve with nits** — fine to merge, minor suggestions only
- 🟡 **Request changes (minor)** — small fixes needed before merge
- 🔴 **Request changes (blocking)** — critical issues that must be addressed

---

After presenting the review, ask the user:
> "Would you like me to open/update the GitHub PR with this review as a comment, fix any of the flagged issues, or do anything else?"
