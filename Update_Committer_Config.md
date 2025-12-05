# Update Committer Config

This document outlines the steps to update the committer configuration for a project repository. You maybe commited changes with different (or invalid) user information, so, you could not push those changes to the remote repository. To fix this, you need to update the committer configuration.

## If all commits are only local (never pushed)

### Update the Git global/local config

```bash
git config --global user.name "<user_name>"
git config --global user.email "<user_email>"
```

Or only for the current repo:

```bash
git config user.name "<user_name>"
git config user.email "<user_email>"
```

---

## Rewrite all commits with correct author/committer

If you want to correct **all commits in the branch**, use:

```bash
git filter-branch --env-filter '
export GIT_AUTHOR_EMAIL="atalay.pabuscu@vestel.com.tr"
export GIT_COMMITTER_EMAIL="atalay.pabuscu@vestel.com.tr"
' -- --all
```

or if you have `git-filter-repo` installed:
```bash
git filter-repo --email-callback '
return b"atalay.pabuscu@vestel.com.tr"
'
```

## Rewrite the last N commits with correct author/committer
For exmample if you have around 20+ commits, so run:

```bash
git rebase -i HEAD~25
```

Replace `25` with however many commits you want to fix.

When the interactive editor opens, change `pick` → `edit` for each commit you want to fix.

For each commit:

```bash
git commit --amend --author="<user_name> <<user_email>>" --no-edit
git rebase --continue
```

### After finishing all commits:

```bash
git push origin <branch> --force
```

⚠ If you did not mark commits as `edit` in the todo file, Abort the rebase:
```bash
git rebase --abort
```

## Check commit author from terminal

```bash
git log --pretty=format:"%h %an <%ae>" -n 25
```

You should see something like:
```
443b859 Atalay Pabuscu <atalay.pabuscu@gmail.com>
...
```

## Clear VSCode Git cache folder if required
```
Ctrl + Shift + P → Git: Clear Git Input History
Ctrl + Shift + P → Git: Refresh
```

---
