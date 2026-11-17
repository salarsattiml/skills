---
name: review-doc
description: Write docs/tmp/review.md, a map of every uncommitted change in review-ordered groups, marking fully staged files as reviewed.
disable-model-invocation: true
---

Write `docs/tmp/review.md`, under the repo root unless the user names another path: a map of every uncommitted change (staged, unstaged, and untracked), grouped by concern and ordered the way the code runs, with a section per file on what it does and what changed.

**Staging is the checkmark.** The user marks a file reviewed by staging it, so git's index is the only record of review progress, and a re-run after a round of `git add` refreshes it. Read the index and leave staging to the user.

**Write for a newcomer.** The reader can read code but knows very little about this repo, so use plain language throughout and explain every repo-specific word under Terms.

## Process

### 1. Collect the change set

From the repo root (`git rev-parse --show-toplevel`), run in parallel:

- `git status --porcelain --untracked-files=all`: every changed path, with its index status in the first column and its worktree status in the second. `--untracked-files=all` lists each file inside an untracked folder instead of the folder alone.
- `git diff HEAD --numstat`: lines added and removed per tracked file, with `-` marking a binary file.

Leave `docs/tmp/review.md` itself out of the change set. If `git ls-files docs/tmp/review.md` prints it, the file is tracked: stop and ask the user where to write instead. If nothing else has changed, say so and stop without writing.

**Done when:** every status line except `docs/tmp/review.md` is a file to write up.

### 2. Read each file

Write four blocks per file, built around the file's **units**: classes and functions in code (a method is `Class.method`), headings in docs, top-level keys in config.

- **Background**: what the file does at a high level, in relation to the rest of the repo, then one headlined bullet per purpose the code inside it serves.
- **What it does**: each unit and its job.
- **Added** and **Removed**: what the change adds and takes away, unit by unit, from `git diff HEAD -- <path>`. Write `None` under whichever of the two is empty.

Read the file itself to write them; for a deleted file, read `git show HEAD:<path>`. In a new file every unit is added, so Added describes each one in full; in a deleted file every unit is removed, so Removed does. A binary file (an image, an archive) gets only a Background, naming its kind.

Past roughly 30 files, fan the reading out to subagents in batches of neighboring files (a directory, or a slice of a large one). Give each this step, the review.md structure section, the newcomer rule, and the full list of changed files, and have it return the blocks for its files, the repo-specific words they use, and what each file uses from the other changed files, in the order its code runs.

**Done when:** every block comes from reading the file and its diff, never from the file's name alone, and every unit that changed (all of them, in a new or deleted file) is described under Added or Removed.

### 3. Group and order

Cluster the files into groups by concern: files that changed for one reason (a feature, a fix, a refactor, a dependency bump) get reviewed together. Name each group for what it accomplishes and tag it with one category: feature, fix, refactor, test, config, docs, chore, or generated.

Order the files inside each group in **execution order**: the order a debugger stepping through the change would first reach them. Start the walk at the entrypoint that runs the changed code (a route handler, a CLI command, a UI event handler, a queue consumer), even when the entrypoint itself is unchanged. The walk reaches a file when it steps into the file's code, or, for a file nothing steps into (a type, a schema, a migration, a constant), when the code first uses the data that file defines.

- When the change has more than one entrypoint or the code branches, walk the happy path first, then each other path, skipping files already placed.
- Tests go after the code they exercise, and files the walk never reaches (docs, CI config) go last.

Number the groups by the same walk, in the order it first reaches each one; when the change set holds unrelated changes, walk the riskiest first. Groups the walk never reaches come after those it does, and low-signal groups go last: lockfiles, generated or vendored files, binary assets, formatting-only changes.

**Done when:** every file sits in exactly one group, every file the walk reaches sits where the walk first reaches it, and each group has its number plus a one-line reason for sitting where it does.

### 4. Write review.md

Write `docs/tmp/review.md` following the structure below, creating `docs/tmp/` if it is missing and overwriting any earlier copy.

Then check the file at `git rev-parse --git-path info/exclude` for a `/docs/tmp/review.md` line, and append one if it is missing. That file is git's local-only ignore list: it keeps `review.md` out of `git status` and out of a blanket `git add .` without touching any tracked file.

**Done when:** every file from step 1 has a row in its group's table and a section of its own, the progress count equals the number of `true` cells, every repo-specific word in `review.md` has an entry under Terms, and every use of one is a bold link to an anchor that exists there.

### 5. Report

Tell the user where `review.md` is, the groups in review order, and the progress count.

## review.md structure

<review-template>

# Review

<One paragraph: a sentence on what this repo is for, then what the change set does as a whole, and why.>

**Progress:** <reviewed> of <total> files reviewed. Stage a file once you've reviewed it, then re-run `/review-doc`.

## Terms

- <a id="term-<slug>"></a>**[<term>](../../<file>#L<line>)**: <what it means>.
- <a id="term-<other-slug>"></a>**[<other term>](../../<file>#L<line>)**: <what it means, linking any term it uses, as in **[<term>](#term-<slug>)**>.

## 1. <Group name> (<category>)

<One line: where the walk enters this group, or, for a group it never reaches, why the group sits here.>

| File | Reviewed |
| --- | --- |
| [`<path>`](../../<path>) | true |

### [`<path>`](../../<path>)

<details>
<summary><strong>Background</strong></summary>

<what the file does at a high level, in relation to the rest of the repo>

- **<purpose headline>**
  - <what this part of the code does>

</details>

<details>
<summary><strong>What it does</strong></summary>

- [`<Class>`](../../<path>#L<line>)
  - <one sentence: what the class does in relation to the repo>
  - [`<Class.method>`](../../<path>#L<line>)
    - <what the method does, one point per bullet>
- [`<function>`](../../<path>#L<line>)
  - <what the function does, one point per bullet>

</details>

<details>
<summary><strong>Added</strong></summary>

- [`<Class>`](../../<path>#L<line>)
  - <what was added to the class, one point per bullet>
  - [`<Class.method>`](../../<path>#L<line>)
    - <what was added to the method, one point per bullet>
- [`<function>`](../../<path>#L<line>)
  - <what was added to the function, one point per bullet>

</details>

<details>
<summary><strong>Removed</strong></summary>

- `<Class>`
  - <what was removed from the class, one point per bullet>
  - `<Class.method>`
    - <what was removed from the method, one point per bullet>
- `<function>`
  - <what was removed from the function, one point per bullet>

</details>

<One section like this per file, in table order, and one group like this per concern, numbered in review order.>

</review-template>

**Folds** keep each file's section short: every block sits in a `<details>` element, collapsed until the reader clicks its summary. Leave a blank line after each `</summary>` and before each `</details>`, or the markdown inside shows up as raw text. A block whose whole content is one short line (`None`, or a binary file's Background) skips the fold and reads as `**Removed:** None`.

**Links** are relative to the folder `review.md` sits in, so from `docs/tmp/` every repo path climbs two levels first: `../../<path>` for a file, `../../<path>#L<line>` for the line where a unit or definition starts, and `#term-<slug>` for an entry under Terms. Link every file and unit that still exists; name a deleted one in backticks, since it has no line left to point at.

**Terms** explains every word `review.md` uses that this repo coined or uses in its own sense: domain terms, internal module or service names, codenames, acronyms. The reader already knows general programming vocabulary. Base each explanation on the repo's own definition where one exists (a glossary such as `GLOSSARY.md` or `CONTEXT.md`, a README, other docs, or the code that introduces the word); otherwise explain the word from how the code uses it. Leave the section out when no such word appears.

Each entry opens with an anchor whose id is `term-` plus the term lowercased, with each run of characters other than letters and digits turned into one hyphen and any hyphen at the ends dropped (`action ID` becomes `term-action-id`, `` `view` / `limitedView` `` becomes `term-view-limitedview`), and its bold term links to the line in the repo that defines it. Every other use of a term in `review.md`, inside other entries too and in any form (`action IDs` for `action ID`), is a bold link to that anchor: `**[action IDs](#term-action-id)**`. Mentions of a term inside its own entry stay plain.

**Reviewed** is `true` only when every change to the file is staged: in its status line the index column holds a letter and the worktree column is blank (`M `, `A `, `D `, `R `). Every other file gets an empty cell, including unstaged changes (` M`, ` D`), untracked files (`??`), and partly staged files (`MM`, `AM`), whose edits made after staging are still unreviewed.

**Renames** show as `` `<new>` (from `<old>`) `` in the table and the section heading.
