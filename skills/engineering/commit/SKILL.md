---
name: commit
description: Generate a short and long commit message optionally allows the user to commit (requires files to be manually staged).
disable-model-invocation: true
---

Run the following git commands in parallel to understand the current state of the repo:

- `git status` — show staged and unstaged files (If they are no files, give an error message)
- `git diff HEAD` — show all changes (staged and unstaged)
- `git log --oneline -10` — show recent commit history for style reference
- `git branch --show-current` — get the current branch name

Then do the following:

1. **Summarize the changes** in plain English — what was added, changed, or removed and why. Keep it concise (one short paragraph). Focus on the _purpose_ of the changes, not just the file names.

2. **Check branch name**

   Always show the current branch name: "We are commiting the changes to branch [current_branch]"
   - If the branch name does not match the nature of the changes, warn the user before proceeding:
     - ⚠️ **Warning:** The current branch (`<branch-name>`) doesn't seem to match the staged changes.
     - Ask: "Do you want to continue anyway, or switch branches first?"
     - Wait for confirmation before proceeding.

3. **Stage any unstaged changes**
   - If there are unstaged changes relevant to this commit, run `git add .` before committing.

4. **Present two commit message options** and ask the user to choose:

   **Detailed:**

   ```
   git commit -m "<top-line summary>

   - <change 1>
   - <change 2>
   - <change 3>"
   ```

   - Top line: concise summary (50 chars or less ideally)
   - Bullets: 2–5 key changes, fewer is better

   **Single-line:**

   ```
   git commit -m "<top-line summary>"
   ```

   Then ask: "Does this look good? Reply **yes** (and specify `detailed` or `single`) to commit, **edit** to change the message, or **no** to cancel."

5. **Wait for the user's response:**
   - **yes + detailed** → commit using the detailed message
   - **yes + single** → commit using the single-line message
   - **yes** (no preference stated) → use the single-line message
   - **edit** → ask the user what they'd like to change, update the message, and re-present for approval
   - **no / cancel** → do nothing

Do NOT commit until the user explicitly approves.
