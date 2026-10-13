---
name: write-tests
description: Write tests with specified style
---

Run the following git commands in parallel to identify recently generated/modified code:
- `git diff HEAD` — show all unstaged changes
- `git diff --cached` — show staged changes
- `git diff HEAD~1 HEAD -- '*.py'` — show files changed in last commit

Then do the following:

1. **Identify the target files** — collect all source files that were added or modified. Exclude test files, config files, lockfiles, and markdown.

2. **Read each target file** in full to understand:
   - What functions, classes, or modules were added or changed
   - What the expected inputs, outputs, and edge cases are
   - Any existing tests nearby (check sibling `tests/`, `__tests__/`, `*.test.*`, `*_test.*` files)

3. **Test framework** in use:
   - `pytest` is the test framework (but also uses some functionality from `unittest`) in existing test files or config (`pyproject.toml`, `package.json`, etc.)
   - Match the style, imports, and conventions of existing tests

4. **Write or update tests** that cover:
   - Think deeply about what each new functionality is doing before writing tests

   Style:
   - Happy path for each public function/method/endpoint
   - Edge cases and boundary conditions
   - Error handling and invalid inputs
   - Any async behavior, if applicable
   - Make sure to add a brief comment to each test

   Follow these rules:
   - When writing a multiple tests for a function - create a class and put all tests in that class
   - When writing multiple tests for a class - create a class and put all tests in that class
   - Place tests in the appropriate test file (create one if none exists, following project conventions)
   - Mirror the module structure of the source file
   - Use descriptive test names that explain *what* is being tested and *expected behavior*
   - Do NOT over-mock — only mock external I/O (network, DB, filesystem)
   - Keep each test focused on one behavior

5. **Run the tests**
   - For each new file tests were added to run `pytest [filename]`

6. **Show the user a summary** of:
   - Which files were analyzed
   - Which test file(s) were created or updated
   - A brief list of test cases added (one line each)

6. **Ask the user:**
   > "Tests written. Would you like me to adjust coverage, or add any specific cases?"
