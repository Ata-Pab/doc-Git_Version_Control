# Standard Workflow

### Step 1: Update your branch

```sh
git fetch
git rebase origin/main
```

### Step 2: Develop

```sh
git add -p
git commit -m "Implement speed control"
```

### Step 3: Submit/Push Changes

**Gerrit (for review)**:

```sh
git push origin HEAD:refs/for/main
```

**GitHub / GitLab (direct push)**:

```sh
git push origin HEAD:main
```

### Step 4: After review comments (Gerrit)

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
