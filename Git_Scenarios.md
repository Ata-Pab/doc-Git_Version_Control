

# Git Scenarios

<details>
  <summary>
    <p style="font-size:18px; display:inline">“I rebased, and now my branch looks broken”</p>
  </summary>
    <br/>

You did:

```sh
git pull --rebase
```

or

```sh
git rebase origin/main
```

After the rebase:

* Some commits **disappeared**
* Or commits got **duplicated**
* Or **conflicts** appear that didn't exist before

Rebase rewrites commit hashes. If during rebase **you already had merges or a messy history**, Git may:

* Drop commits (if they’re already applied)
* Duplicate commits (if commit diff is slightly different)
* Replay commits in a different order

This is common when:

* You had merge commits on your feature branch
* Someone force-pushed the branch before you rebased

### Fix

If everything went wrong, undo the entire rebase:

```sh
git rebase --abort
```

If you already completed the rebase and pushed (bad):

```sh
git reflog
git checkout <previous-safe-hash>
git branch rescue
```

Then reset your branch:

```sh
git checkout feature
git reset --hard <previous-safe-hash>
```

### ✔ Prevention

* **Always fetch + rebase on clean (non-merged) branches**
* Never mix merge commits with rebasing
* Avoid multi-person ownership of feature branches

</details>

---

<details>
  <summary>
    <p style="font-size:18px; display:inline">“I need to split my commit into smaller ones”</p>
  </summary>
    <br/>

You made one large commit containing:

* Logic change
* Refactoring
* Renaming
* New features

Reviewer wants:

* Commit 1: refactor
* Commit 2: logic change
* Commit 3: new feature

Large commits reduce review quality and break bisectability.

### Fix

```sh
git rebase -i HEAD~1
```

Change `pick` to `edit`:

```
edit <hash> large commit
```

When Git stops:

```sh
git reset HEAD^
```

Now all changes are unstaged.

Stage what belongs to commit #1:

```sh
git add -p
git commit -m "refactor: ..."
```

Stage what belongs to commit #2:

```sh
git add -p
git commit -m "feature: ..."
```

Continue:

```sh
git rebase --continue
```

### ✔ Prevention

* Stage-as-you-work (`git add -p`)
* Commit frequently with small diffs

</details>

---

<details>
  <summary>
    <p style="font-size:18px; display:inline">“CI build fails after merging, but works locally”</p>
  </summary>
    <br/>

You merged your feature branch to `main` (or Gerrit did it automatically), but CI shows:

* Build broken
* Tests failing
* Missing files

While your local build passes.

### Possible reasons

1. You didn’t rebase on latest `main` before pushing.
2. Your branch compiled with older headers/config.
3. Another developer pushed changes between your fetch and your push.
4. You had local ignored files making your environment “too clean”.

### Fix

Reproduce CI state locally:

```sh
git fetch
git checkout main
git reset --hard origin/main
```

Then rebuild.

If failing:

* Fix it
* Create hotfix PR / Gerrit change

### ✔ Prevention

* Always `git pull --rebase` before push
* Add `.gitignore` for build directories
* Build on clean workspace (`git clean -fdx`) occasionally

</details>

---

<details>
  <summary>
    <p style="font-size:18px; display:inline">“How to resolve merge conflicts in feature branch?”</p>
  </summary>
    <br/>

Git has two different ways to synchronize your branch with another branch:
- **Merge**-based synchronization: Keeps original commits
- **Rebase**-based synchronization: Rewrites your commits

### **Merge synchronization**

You fetch the latest `main`:

```sh
git fetch origin main
```

Then try to merge:

```sh
git merge origin/main
```

Now, Git notifies you of conflicts:

```Auto-merging file.cpp
CONFLICT (content): Merge conflict in file.c
```

### Resolve

- Edit file (resolve `<<<<<<<`, `=======`, `>>>>>>>` markers)
- Then:

```sh
git add file.c
git merge --continue
```

If you want to abort the merge:

```sh
git merge --abort
```

### **Rebase synchronization**

```sh
git rebase main
```

If a conflict occurs:

```
CONFLICT (content): ...
```

### Resolve

```sh
git add file.c
git rebase --continue
```

### Abort:

```sh
git rebase --abort
```

### Skip problematic commit:

```sh
git rebase --skip
```

### Re-run specific commit with rebase interactive:

```sh
git rebase -i main
```

| Sync Method | History Style              | When Conflict Happens | How to Continue         |
| ----------- | -------------------------- | --------------------- | ----------------------- |
| **merge**   | non-linear (merge commits) | during merge process  | `git merge --continue`  |
| **rebase**  | linear (rewritten history) | during commit replay  | `git rebase --continue` |


</details>

---

<details>
  <summary>
    <p style="font-size:18px; display:inline">“I amended a commit and now the patchset disappeared / Gerrit uploaded new review instead of updating”</p>
  </summary>
    <br/>

You expected:

```
Patchset #2
```

But Gerrit created a brand new change.

### Reason

You accidentally removed or changed the **Change-Id** line inside the commit message.

Example missing line:

```
Change-Id: Ia7f32dc1f...
```

Without it, Gerrit thinks it’s a different change → new code review.

### Fix

1. Find the Change-Id from Gerrit UI
2. Edit commit message:

```sh
git commit --amend
```

Add the original Change-Id manually.

Push again:

```sh
git push origin HEAD:refs/for/main
```

### ✔ Prevention

* Never delete Change-Id
* Always use `git commit --amend`, not new commits
* Use Gerrit’s commit-msg hook to auto-generate Change-Id

</details>

---

<details>
  <summary>
    <p style="font-size:18px; display:inline">“I force-pushed and deleted my teammate’s commits”</p>
  </summary>
    <br/>

Developer A:

```
git push
```

Developer B:

```
git push --force
```

→ Deletes A’s commits on the remote.

### Reason

Force push **rewrites** remote branch history.

### Fix

Recover deleted commits via reflog:

```sh
git reflog
```

Find the hash and restore:

```sh
git checkout main
git reset --hard <restore-commit>
git push --force-with-lease
```

### ✔ Prevention

* Disable force-push on shared branches
* Only force-push on:

  * Your own feature branches
  * Gerrit refs/for branches

</details>

---

<details>
  <summary>
    <p style="font-size:18px; display:inline">“I cherry-picked a fix but now I see the same fix twice after merging branches”</p>
  </summary>
    <br/>

You cherry-picked a fix from develop → release.

Then later you merged develop → release.

> Git applies the same changes again, but the hashes differ (new commit vs cherry-picked one).

### Reason

Cherry-pick creates a **duplicate commit** with a different hash.
Merge sees the fixes as different commits → duplicated history.

### Fix

There are two ways:

### 1. Use `git revert`

Revert the cherry-pick duplicate commit.

```sh
git revert <duplicate-commit-id>
```

### 2. Use merge strategy “ours” or “theirs”

During merge conflict state:

```sh
git checkout --theirs .
git add .
git commit
```

### ✔ Prevention

* Cherry-pick only hotfixes that will also be merged back
* Always merge hotfix branch back to develop
* Avoid cherry-picking commits you will later merge from the source branch

</details>

---

<details>
  <summary>
    <p style="font-size:18px; display:inline">“I applied a patch (`git apply`) but commit doesn't show in history”</p>
  </summary>
    <br/>

You used:

```sh
git apply fix.patch
```

This only applies **diff to working directory**, NOT a commit.

### Reason

You need to use:

```sh
git am fix.patch
```

If patch has metadata (author/date/msg), `git am` preserves it.

### ✔ Prevention

Use `git apply` only when:

* You want raw changes
* You will commit manually

Use `git am` when:

* You want to import a commit cleanly

</details>

---
