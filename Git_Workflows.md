# Contents

- [Standard Workflow](#standard-workflow)
- [Working with Patches (Gerrit Workflow)](#working-with-patches-gerrit-workflow)
  - [1. Exporting a patch](#1-exporting-a-patch)
  - [2. Applying a patch](#2-applying-a-patch)
  - [3. Using patches for code review (Gerrit workflow)](#3-using-patches-for-code-review-gerrit-workflow)
- [Working with GitHub Issues and Pull Requests](#working-with-github-issues-and-pull-requests)
  - [Create a feature branch for the issue](#create-a-feature-branch-for-the-issue)
  - [Work → stage → commit](#work--stage--commit)
  - [Push the branch to GitHub](#push-the-branch-to-github)
  - [Open a Pull Request (PR)](#open-a-pull-request-pr)
  - [Gerrit Workflow](#gerrit-workflow)
  - [Recommended advanced patterns](#recommended-advanced-patterns)
- [Opening pull request in CLI](#opening-pull-request-in-cli)
- [Integrate GitHub issues with VSCode](#integrate-github-issues-with-vscode)
  - [View & Manage Issues Inside VSCode](#view--manage-issues-inside-vscode)
  - [Search and Filter Issues (VSCode Query Syntax)](#search-and-filter-issues-vscode-query-syntax)
  - [Create a New Issue from VSCode](#create-a-new-issue-from-vscode)
  - [Open an Issue for Editing / Discussion](#open-an-issue-for-editing--discussion)
  - [Link a Commit to a GitHub Issue via VSCode](#link-a-commit-to-a-github-issue-via-vscode)
  - [Use GitHub Workflow Commands Inside VSCode](#use-github-workflow-commands-inside-vscode)
  - [One-Click PR From Assigned Issue](#one-click-pr-from-assigned-issue)
  - [Auto-Create Branch From Issue](#auto-create-branch-from-issue)
  - [Complete Workflow](#complete-workflow)
- [Difference between `git pull --rebase` vs `git pull origin`](#difference-between-git-pull---rebase-vs-git-pull-origin)
- [“Hotfixes” in Git workflows](#hotfixes-in-git-workflows)
  - [Hotfix branch](#hotfix-branch)
  - [Hotfix in Gerrit-driven workflows](#hotfix-in-gerrit-driven-workflows)
- [Working with worktrees](#working-with-worktrees)
  - [Creating a worktree](#creating-a-worktree)
  - [Working inside a worktree](#working-inside-a-worktree)
  - [Committing changes in a worktree](#committing-changes-in-a-worktree)
  - [Merging a worktree's branch back](#merging-a-worktrees-branch-back)
  - [Listing, moving and removing worktrees](#listing-moving-and-removing-worktrees)
  - [Worktrees vs. branches — theoretical differences](#worktrees-vs-branches--theoretical-differences)
- [Working with submodules](#working-with-submodules)
  - [Adding a submodule](#adding-a-submodule)
  - [Cloning a repo that has submodules](#cloning-a-repo-that-has-submodules)
  - [Updating submodules](#updating-submodules)
  - [Making changes inside a submodule](#making-changes-inside-a-submodule)
  - [Removing a submodule](#removing-a-submodule)
  - [Submodules vs. worktrees vs. branches](#submodules-vs-worktrees-vs-branches)

---

# Standard Workflow

### Step 1: Save your changes in stash
```sh
git stash push -u -m "latest_20072026"
```

### Step 2: Update your branch

```sh
git fetch origin
git rebase origin/main
```

### Step 3: Develop

```sh
git add -p
git commit -m "Implement speed control"
```

### Step 4: Submit/Push Changes

**Gerrit (for review)**:

```sh
git push origin HEAD:refs/for/main
```

**GitHub / GitLab (direct push)**:

```sh
git push origin HEAD:main
```

### Step 5: Pop stash, get your changes
```sh
git stash pop
```

### Step 6: After review comments (Gerrit)

```sh
git add fix.c
git commit --amend --no-edit
git push origin HEAD:refs/for/main
```

**Gerrit** automatically creates a *patch set* using Change-ID.

---

# Working with Patches (Gerrit Workflow)

Git **patch** files represent changes as **textual diffs** that can be e-mailed, exported, applied, or reviewed offline.

### Where Git patches are used:

* Gerrit `git push origin HEAD:refs/for/branch`
* GitHub PR emails
* GitLab/MR reviews via email
* Sending patches via mail (`git format-patch` / `git send-email`)
* Code reviews in mailing-lists (Linux kernel, U-Boot, Zephyr)
* CI-based auto-patch workflows

There are three major patch operations:

## 1. Exporting a patch

Export commits as `.patch` files:

```sh
git format-patch -1 <commit>
git format-patch origin/main..HEAD
```

Each patch contains:

* Metadata (author, subject, message)
* Diff of file changes
* Optional Change-Id (when using Gerrit)
* References to parent commit

### Example:

```sh
git format-patch -3
```

Exports the last 3 commits as 3 numbered `.patch` files.

---

## 2. Applying a patch

Apply patch as-is:

```sh
git apply myfix.patch
```

* Applies diff into the working directory.
* Does not create a commit.

Apply patch as a commit:

```sh
git am myfix.patch
```

* Applies the patch **and creates a commit** reproducing original author info.

---

## 3. Using patches for code review (Gerrit workflow)

In the Gerrit world, patchsets are **essentially commits that share the same Change-Id**.

Updating the same patchset:

```sh
git commit --amend
git push origin HEAD:refs/for/main
```

Gerrit shows this as Patchset #2, #3, etc.

---

### Git patches are used:

- Gerrit git push origin HEAD:refs/for/branch
- GitHub PR emails
- GitLab/MR reviews via email
- Sending patches via mail (git format-patch / git send-email)
- Code reviews in mailing-lists (Linux kernel, U-Boot, Zephyr)
- CI-based auto-patch workflows

### `git format-patch`

Creates files like:

```
0001-fix-bug.patch
```

### `git apply` / `git am`

Apply patches from files or emails.

So patches are **a universal Git mechanism for representing commits**.

---

# Working with GitHub Issues and Pull Requests

Always begin from your updated main branch:

```sh
git checkout main
git pull origin main
```

### Create a feature branch for the issue

Use this naming convention:

```sh
git checkout -b feature/<issue-number>-<short-description>
```

Examples:

```sh
git checkout -b feature/123-fix-login-bug
git checkout -b feature/98-add-device-driver
git checkout -b feature/450-improve-cache-performance
```

---

### Work → stage → commit

Stage your changes:

```sh
git add .
```

Write a commit that **links to the GitHub issue**:

### > Option A – Close issue when PR merges:

```sh
git commit -m "Fix login bug (#123)"
```

Or more explicit:

```sh
git commit -m "Fix: Login page crash on invalid token

Closes #123"
```

### > Option B – Just reference the issue (does not auto-close):

```sh
git commit -m "Refactor login handler to improve error handling (#123)"
```

GitHub automatically links these commits/issues.

---

### Push the branch to GitHub

```sh
git push -u origin feature/123-fix-login-bug
```

This creates a remote branch.

---

### Open a Pull Request (PR)

If you use GitHub CLI:

```sh
gh pr create --fill
```

Or with an interactive prompt:

```sh
gh pr create --title "Fix login bug (#123)" --body "This PR fixes issue #123"
```

If using browser:
GitHub will show a “Compare & Pull Request” banner.

### Ensure your PR description includes:

```
Closes #123
```

or

```
Fixes #123
```

This ensures GitHub automatically closes the issue when PR merges.

> You can create PR to another branches as well (e.g., `develop`, `staging`).

```sh
gh pr create --base develop --head feature/123-device-driver
```

In GitHub web UI, select the base branch accordingly.

```
base: develop
compare: feature/123-device-driver
```

### Gerrit Workflow

Gerrit has no “pull requests”, but it has “patch sets” and code reviews.
Target branches are flexible:

```sh
git push origin HEAD:refs/for/develop
git push origin HEAD:refs/for/release/2.1
git push origin HEAD:refs/for/feature/camera
```

## Recommended advanced patterns

### > Option A – Branch protection requires rebase

If your repo requires rebasing before push:

```sh
git pull --rebase origin main
git push --force-with-lease
```

> Use `--force-with-lease`, not `--force`, to avoid overwriting work by others.

---

### > Option B – Use conventional commits

Example:

```sh
git commit -m "fix(auth): prevent login crash (#123)"
```

---

### > Option C – Work on multiple commits

Interactive rebase before making the PR:

```sh
git rebase -i main
```

Squash, reorder, or clean your commits.

---

# Opening pull request in CLI

Git itself does **not** create PRs. But:

### **GitHub**

```bash
gh pr create
```

### **GitLab**

```bash
glab mr create
```

### **Gerrit**

"Pull request" = **Change / patchset**
Created by:

```bash
git push origin HEAD:refs/for/<branch>
```

Gerrit then expects reviewers to +2 Code-Review & +1 Verified.

---

# Integrate GitHub issues with VSCode

Install Required Extensions: **GitHub Pull Requests and Issues** (official from GitHub)

This single extension gives you:

* Issue browser
* Create/close issues
* Add comments & labels
* Create pull requests
* Review PRs
* Checkout PR branches

**Sign In with GitHub**

```
Ctrl + Shift + P → “GitHub: Sign in”
```

This connects VSCode to your GitHub account.

### View & Manage Issues Inside VSCode

**Open the GitHub Panel**

Left sidebar → you will see a GitHub icon:

```
GITHUB
  • Pull Requests
  • Issues
```

Click **Issues**.

You can now see:

* Issues assigned to you
* Issues you created
* Issues in your repository
* Open or closed issues
* Issues filtered by label, milestone, etc.

### Search and Filter Issues (VSCode Query Syntax)

VSCode uses the same search syntax as GitHub.

Examples:

```
assignee:@me is:open
author:@me label:bug
is:open milestone:v1.2
```

### Create a New Issue from VSCode

Use command palette:

```
Ctrl + Shift + P → GitHub: Create Issue
```

Or right-click on a file/line of code:

```
Add issue
```

You can set:

* Title
* Description
* Labels
* Assignees
* Milestones

VSCode uses Markdown for issue description.

### Open an Issue for Editing / Discussion

Click an issue → opens as a VSCode editor tab.

You can:

* Comment
* React
* Add labels
* Close issue
* Add assignees
* Add linked PRs

### Link a Commit to a GitHub Issue via VSCode

If your commit message references an issue number, GitHub auto-links it.

Examples:

```
Fix #23
Closes #23
Implements #23
```

VSCode helps by autocompleting issue numbers in commit messages.

When you type:

```
#2
```

VSCode will show a popup listing issues that match.

### Use GitHub Workflow Commands Inside VSCode

You can run GitHub Actions workflows, or view logs inside VSCode.

Command:

```
GitHub: View Workflow Runs
```

### One-Click PR From Assigned Issue

When you open an issue, VSCode suggests:

```
Create Pull Request from Issue
```

This automatically creates:

* A branch
* Pre-fills PR title & description
* Links PR to issue
* Assigns reviewers
* Adds labels

This is the most seamless GitHub integration workflow.

### Auto-Create Branch From Issue

You can also create a branch named after an issue:

```
GitHub: Create Branch
```

VSCode proposes:

```
feature/123-fix-bug
```

Where `123` is the issue ID.

## Complete Workflow

Inside VSCode:

### 1. Pick an assigned issue

Left sidebar → GitHub → Issues → Assigned to me

### 2. Create a feature branch

Right-click → "Create branch"

VSCode auto-generates:

```
feature/123-short-description
```

### 3. Implement → commit

Commit and push. VSCode auto-suggests linking issue numbers.

### 4. Open PR directly from VSCode

GitHub panel → Pull Requests → “Create Pull Request”

### 5. Review comments and fix directly

Inline comments appear in VSCode, same as Gerrit code review.

---

## Difference between `git pull --rebase` vs `git pull origin`

### **`git pull` = fetch + merge**

```bash
git pull origin main
```

is equivalent to:

```
git fetch origin main
git merge origin/main
```

Creates a **merge commit** if histories diverged.

### **`git pull --rebase`**

```bash
git pull --rebase
```

is equivalent to:

```
git fetch
git rebase origin/your-branch
```

This **rewrites** your local commits on top of the remote, **avoiding merge commits**.

`git pull --rebase` reduces conflicts because your commits are replayed one by one, instead of mixing the history into a three-way merge.
Rebasing surfaces conflicts on a *per-commit* basis — easier to solve cleanly.

You should use `git pull --rebase` in Gerrit. Because Gerrit expects a **clean linear history** and avoids unnecessary merge commits.

---

# “Hotfixes” in Git workflows

A **hotfix** is a quick, urgent fix applied to production, typically outside normal feature workflows.

Hotfixes appear in:

* **Git Flow**
* **GitHub Flow**
* **Enterprise branching strategies**

---

### Hotfix branch

Example using Git Flow:

```
main        -- deployment / production
develop     -- next release in progress
feature/*   -- new features
hotfix/*    -- urgent fixes to production
release/*   -- release staging
```

### Scenario:

Production has a critical bug.
You must fix it **without waiting** for ongoing development.

### You create:

```
git checkout -b hotfix/fix-crash main
```

After fixing:

```
git commit -m "Fix crash"
git checkout main
git merge hotfix/fix-crash
git tag -a v1.2.1
git checkout develop
git merge hotfix/fix-crash
```

Production is patched immediately, while `develop` also receives the fix.

Hotfix = minimal patch applied on top of stable code.

### Hotfix in Gerrit-driven workflows
You usually:

1. Checkout your production branch
2. Create a hotfix branch
3. Submit patch to Gerrit
4. Review → Approve → Merge
5. Cherry-pick into the development branch if needed

---

# Working with worktrees

A **worktree** is a separate working directory that is linked to the same Git repository (the same `.git` history, objects, and refs). It lets you have **multiple branches checked out at the same time**, each in its own folder, without cloning the repo again and without stashing/switching in your current folder.

Typical use cases:

* Fixing an urgent bug while a feature branch is mid-work (uncommitted changes) and you don't want to stash it.
* Running a long build/test on one branch while continuing to code on another.
* Reviewing a colleague's PR branch side-by-side with your own work.
* Keeping a permanent `main` worktree for deployments while developing in another.

---

## Creating a worktree

Add a worktree for an **existing** branch:

```sh
git worktree add ../project-hotfix hotfix/fix-crash
```

Add a worktree and create a **new** branch at the same time:

```sh
git worktree add -b feature/123-fix-login-bug ../project-login origin/main
```

* `../project-login` — path where the new working directory is created (outside or inside the repo's parent folder, but **not** inside the main worktree itself).
* `feature/123-fix-login-bug` — the new branch name.
* `origin/main` — the starting point (commit-ish) for the new branch.

Add a worktree in a **detached HEAD** state (no branch, e.g. for a quick look at a tag or commit):

```sh
git worktree add --detach ../project-check v1.2.1
```

---

## Working inside a worktree

Each worktree behaves like a normal, independent working directory:

```sh
cd ../project-login
git status
git log --oneline -5
```

Only one branch can be checked out per worktree at a time (and a branch can only be checked out in **one** worktree at once — Git blocks checking out `feature/123-fix-login-bug` again elsewhere while it's active here).

---

## Committing changes in a worktree

Commits work exactly as in a regular clone — they're written to the **same shared repository**, just under the branch checked out in that worktree:

```sh
cd ../project-login
git add .
git commit -m "Fix: Login page crash on invalid token"
git push -u origin feature/123-fix-login-bug
```

Because the object database is shared, this commit is immediately visible from your main worktree too:

```sh
cd ../project        # your original/main worktree
git log feature/123-fix-login-bug --oneline -3
```

---

## Merging a worktree's branch back

Merging (or rebasing) is done from whichever worktree has the **target** branch checked out — there is nothing worktree-specific about the merge itself:

```sh
cd ../project              # worktree with 'main' checked out
git merge feature/123-fix-login-bug
```

Or, following the GitHub workflow, open a PR instead:

```sh
cd ../project-login
gh pr create --fill
```

Once merged/deleted upstream, remove the now-unneeded worktree (see below) — Git will refuse to delete a branch that's still checked out in another worktree.

---

## Listing, moving and removing worktrees

```sh
git worktree list
```

```
C:/repos/project          abcd123 [main]
C:/repos/project-login    ef01234 [feature/123-fix-login-bug]
C:/repos/project-hotfix   9876aaa [hotfix/fix-crash]
```

Move a worktree to a new path:

```sh
git worktree move ../project-login ../project-login-old
```

Remove a worktree (directory must be clean, or use `--force`):

```sh
git worktree remove ../project-login
```

If the directory was deleted manually instead of via `git worktree remove`, clean up the stale metadata:

```sh
git worktree prune
```

---

## Worktrees vs. branches — theoretical differences

| | **Branch** | **Worktree** |
|---|---|---|
| **What it is** | A movable pointer (ref) to a commit — pure metadata inside `.git`. | A real, separate directory on disk with its own working files. |
| **Checkout scope** | Switching branches (`git checkout`) changes the **files in the current directory** to match that branch's commit. | Adding a worktree creates an **additional directory**; the current one is untouched. |
| **Concurrency** | Only **one** branch can be active in a given working directory at any moment. Switching branches means your uncommitted changes must be committed, stashed, or carried over. | **Multiple** branches can be checked out **simultaneously**, one per worktree — no stashing needed to work on two branches at once. |
| **Storage cost** | Extremely cheap — a branch is just a 41-byte file pointing to a commit SHA. | More expensive — each worktree has its own full set of checked-out files (though history/objects are still shared, not duplicated). |
| **Repository identity** | All branches belong to and live inside the **same** working directory's `.git`. | All worktrees share the **same** `.git` object database, refs, and config — they are views into one repository, not clones. |
| **Relation to each other** | A branch is a *name*; it doesn't imply any particular directory. | A worktree is a *place*; it always has exactly one branch (or a detached commit) checked out. |
| **Analogy** | Like a bookmark in a shared book — it just marks a page. | Like photocopying the book open to that bookmarked page, so several people can read different pages at once from the same original book. |

---

# Working with submodules

A **submodule** is a Git repository embedded inside another Git repository, checked out at a specific commit. Unlike worktrees (which share one `.git` object database), a submodule is a genuinely **separate repository** with its own `.git`, history, remotes, and branches — the parent repo just records *which commit* of that separate repo to use.

Typical use cases:

* Vendoring a third-party library while keeping the ability to pull upstream updates.
* Sharing common code (drivers, shared libs, protocol definitions) across multiple independent projects.
* Pinning a dependency to an exact, reviewed commit instead of a floating version.

---

## Adding a submodule

```sh
git submodule add https://github.com/example/lib.git libs/example-lib
```

This:

* Clones `lib.git` into `libs/example-lib`.
* Creates/updates a `.gitmodules` file recording the URL and path.
* Stages `libs/example-lib` as a special entry (a *gitlink*) pointing at the submodule's current commit — not its file contents.

Commit it like any other change:

```sh
git add .gitmodules libs/example-lib
git commit -m "Add example-lib as a submodule"
```

---

## Cloning a repo that has submodules

A plain `git clone` leaves submodule directories **empty**. Either clone recursively:

```sh
git clone --recurse-submodules https://github.com/you/project.git
```

Or initialize them afterwards:

```sh
git clone https://github.com/you/project.git
cd project
git submodule update --init --recursive
```

---

## Updating submodules

Pull the exact commit recorded by the parent repo (no changes to submodule branch):

```sh
git submodule update --init --recursive
```

Update a submodule to the **latest** commit on its tracked branch (recorded in `.gitmodules`):

```sh
git submodule update --remote libs/example-lib
```

Then commit the bump in the parent repo — the pointer change is a normal diff:

```sh
git add libs/example-lib
git commit -m "Bump example-lib to latest main"
```

Fetch and update every submodule at once:

```sh
git submodule update --remote --merge
```

---

## Making changes inside a submodule

A submodule is a real repo — `cd` into it and use Git normally:

```sh
cd libs/example-lib
git checkout -b fix/upstream-bug
git commit -am "Fix off-by-one in parser"
git push origin fix/upstream-bug
```

Then go back to the parent repo and record the new commit it should point to:

```sh
cd ../..
git add libs/example-lib
git commit -m "Point example-lib at fix/upstream-bug"
```

> If you forget the second commit, the parent repo still points at the **old** submodule commit — `git status` in the parent will show the submodule as having "new commits" until you stage it.

---

## Removing a submodule

```sh
git submodule deinit -f libs/example-lib
git rm -f libs/example-lib
rm -rf .git/modules/libs/example-lib
git commit -m "Remove example-lib submodule"
```

---
