# Git Commands Cheat Sheet

## `git add`

* Moves changes from the working directory → staging area (index).
* Git commits *only* what’s in the staging area.
* It does **not** store changes in history, just prepares them.

```sh
git add file.c
git add src/
git add .
```

* Add intentionally, not with `.` for large repos.

### Interactive staging (`-p path mode`):
* Lets you interactively stage parts of a file, not the whole file. It breaks changes into “hunks,” and for each hunk you choose: 
  - stage it (`y`)
  - skip it (`n`)
  - split it further (`s`)
  - view help (`?`)

```sh
git add -p
```

When you have mixed changes in a file (debug logs + feature code + formatting), and you want a clean commit history by committing them separately. Git runs the diff engine, shows each diff hunk, and updates the index only for the selected hunks. Nothing changes in the working directory.

---

## `git commit`

* Creates a snapshot of staged changes.
* Stores commit metadata (author, date, message, parent commit).

```sh
git commit -m "Implement motor driver init"
```

---

## `git push`

* Sends local commits → remote branch.
* **Without Gerrit**: updates the remote branch directly.
* **With Gerrit** review workflow: pushes to `refs/for/<branch>` for code review instead of directly updating the branch.

**Standard usage**

```sh
git push origin my-branch
```

**Gerrit usage**

```sh
git push origin HEAD:refs/for/my-branch
```

### `git push --force`

- Force push **overwrites** the remote branch with your local version, **discarding remote history**.

```sh
git push --force
```

**Safer version:**

```sh
git push --force-with-lease
```

This ensures you don’t overwrite someone else’s work.

- Force push is required when you rewrote history:
    - `git rebase`
    - `git commit --amend`
    - `git reset --hard`
    - Interactive rebase (`git rebase -i`)

Because the remote branch’s commit chain no longer matches your local chain.

### Force push in **Gerrit**

If you’re using Gerrit with `refs/for/<branch>`, force-push is **almost never needed**, because:

* Gerrit identifies patchsets by Change-Id.
* Amending commits automatically updates the patchset.

Force push is more relevant when you push directly to a branch (**GitHub** flows).

---

## `git pull`

`git pull` = `git fetch` + `git merge` (or rebase depending config)

* Fetch latest remote references.
* Merge them into your current branch.

```sh
git pull
```

Avoid pull merge conflicts by using rebase:

```sh
git pull --rebase
```

---

## `git fetch`


* Downloads remote branch updates without touching your working branch.
* Safest way to check what changed.

```sh
git fetch
git fetch origin my-branch
```

```sh
git fetch
git log origin/main
```

---

## `git checkout`

**Modern name**: `git switch` (for branches), `git restore` (for files)

* Switch branches:

```sh
git checkout develop
```

* Create + switch:

```sh
git checkout -b new-feature
```

* Checkout specific commit:

```sh
git checkout <commit-hash>
```

* Checkout previous commit:

```sh
git checkout HEAD~1
```

* Checkout a file from a commit:

```sh
git checkout HEAD~1 -- src/main.c
```

* Chekout a tag (you can checkout the tag in a Git **detached HEAD** state)
```sh
git checkout <tag name>
```

* If you want to Git checkout the tag as a branch, you will run:
```sh
git checkout -b <branch name> <tag name>
```

---

## `git amend`

* Overwrites the last commit with a new one.
* Keeps history clean.
* Useful for:

  * Fixing commit message
  * Adding forgotten files
  * Squashing tiny changes into the previous commit

```sh
git commit --amend
```
### **Example**

```sh
git add missing_file.c
git commit --amend --no-edit
```

⚠️ Do **not amend** after pushing to shared remote unless:

* You are in a Gerrit workflow (Gerrit handles patch sets automatically with Change-IDs)
* You **know** how to rebase/force push safely

---

## `git reset`

* Moves your current branch (HEAD) to a different commit (moves the branch pointer backward or forward), rewrites history.

### **Three modes**

#### **1. Soft**

Keeps changes staged.

```sh
git reset --soft HEAD~1
```

#### **2. Mixed (default)**

Unstage but keep changes.

```sh
git reset HEAD~1
```

#### **3. Hard**

⚠️ Destroys working directory changes.

```sh
git reset --hard HEAD~1
```

Undoing a commit before reviewing it:

```sh
git reset --soft HEAD~1
git add -p
git commit
```

---

## `git revert`

* Create a new commit that undoes the changes of a previous commit.
* Does not rewrite history, safe for shared branches.

```sh
git revert <commit-id>
```

---

## `git stash`

* Temporarily store uncommitted changes.
* Useful when switching to another branch quickly without committing your work.

```sh
git stash
git stash apply
git stash pop
```

---

## `git merge`

* Combine histories of two branches.

```sh
git merge my-branch
```

### Merge commit vs fast-forward

* **Fast-forward**: pointer moves forward.
* **Merge commit**: new commit created.

---

## `git rebase`

* Rewrites your local commits so they appear **after** the latest remote commits, creating a clean linear history.
* Avoids unnecessary merge commits.

```sh
git rebase feature-branch
```

**Standard rebase**:

```sh
git checkout feature-branch
git rebase main
```

**Interactive rebase:**

```sh
git rebase -i HEAD~5
```

Used for:

* Squashing commits
* Editing messages
* Cleaning history before review

**Without `--rebase` (default pull = merge):**

```
A --- B -------------- M ← merge commit
        \             /
         \ - C - D - /
```

**With `--rebase`:**

```
A --- B --- C' --- D'
```

Where `C'` and `D'` are your commits replayed on top of the updated base.

- Merge commits (`M`) frequently cause conflicts because Git tries to combine two history lines simultaneously. With `rebase`, you simply **replay** and apply your commits one by one. During rebase, if there's a conflict, it occurs inside **one specific commit**, not across the entire branch.

Solve conflict → continue:

```sh
git add resolved_file.c
git rebase --continue
```

---

## `git log`

* Inspect commit history.

### Essential flags

```sh
git log --oneline --graph --decorate --all
```

### Formatting
```sh
git log --pretty=format:"%h - %an, %ar : %s"
```

- `h` = abbreviated commit hash
- `an` = author name
- `ae` = author email
- `ar` = author date, relative
- `s` = subject
- `d` = ref names (branches, tags)

### Filtering

```sh
git log <file>              # changes related to a specific file
git log --author="<name>"   # filter by author
git log --grep="keyword"    # search commit messages
git log -S "text"           # search for add/remove of a particular string
git log -L :func:file.c     # history of a function in a file (!)
```

### Limiting

```sh
git log -n 20
git log --since="2 weeks ago"
git log --until="2025-01-01"

```

### Diff output with commit history

```sh
git log -p
git log -p --word-diff
git log --stat
```

---

## `git reflog`

* View history of HEAD and branch movements.

```sh
git reflog
```

---

## `git cherry-pick`

* Copy specific commit(s) from anywhere in history and reapply them **as new commits** on your current branch.
* Takes the *diff* of that commit
* Applies it to your current HEAD
* Creates a **new commit** with a different hash

```sh
git cherry-pick <commit-id>
git cherry-pick <commit1> <commit2>
git cherry-pick A..D
```

### Use cases

#### **1. Hotfix flow**

You need a fix from `develop` to `release`:

```sh
git checkout release
git cherry-pick <commit-id>
```

Perfect for release branches.

#### **2. Move a single feature/fix across multiple branches**

Example:

* Fix made in `my-branch` branch
* Need same fix in `my-other-branch`

Cherry-pick avoids merging whole branch history.

```sh
git checkout my-other-branch
git cherry-pick <fix-commit>
```

---

#### **3. Unblock a broken build**

If CI fails on old commit, but someone fixed the issue later:

#### **4. Split large feature into independent commits**

During cleanup before code review.

⚠️ Cherry-picking can cause duplicate commits if used incorrectly:

* One commit appears multiple times with different hashes.

Never cherry-pick arbitrary commits *after merging branches* — leads to repeated code.

---

## `Exit Git CLI Output`

* Press `q` to exit logs or help screens.


## ❓ Questions 




## References

[Git Cheat Sheet](https://git-scm.com/cheat-sheet)

[GitKraken - Git Checkout](https://www.gitkraken.com/learn/git/git-checkout#:~:text=In%20order%20to%20Git%20checkout,followed%20by%20the%20commit%20hash.)

[OpenAI. (2025). ChatGPT 5.](https://chatgpt.com/)