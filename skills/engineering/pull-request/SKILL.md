---
name: pull-request
description: Open a pull request using the github CLI
disable-model-invocation: true
---

Run the following commands in parallel to understand the current branch state:

- `git branch --show-current` — get the current branch name
- `git log --oneline -10` — recent commits for context
- `git remote -v` — confirm the remote repo

Then do the following:

1. **Ask the user which base branch** to open the PR against (e.g. `develop`, `main`). If they provided one as an argument to this command, use that instead.

2. **Run in parallel:**
   - `git log <base>..<current> --oneline` — list commits unique to this branch
   - `git diff <base>...<current> --stat` — summarize what files changed

3. **Draft a PR title and body** based on the branch name and commit history:
   - Title: short, imperative, under 70 characters
   - Body sections:
     - **Summary**: bullet points describing what changed and why
     - **Test plan**: checklist of how to verify the changes

4. **Present the draft** to the user and ask:

   > "Does this PR description look good, or would you like to edit it before opening?"

5. Wait for user response:
   - If **yes / looks good** → push the branch if not already pushed (`git push -u origin <branch>`), then run:
     ```
     gh pr create --base <base-branch> --title "<title>" --body "<body>"
     ```
   - If they **provide edits** → use their version and open the PR
   - If **no / cancel** → do nothing

6. On success, return the PR URL to the user.

Do NOT push or open the PR until the user explicitly approves.
