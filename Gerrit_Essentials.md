# Git – Gerrit Essentials

Gerrit is a Git server that provides access control for the hosted Git repositories.

### Clone Gerrit Project

```sh
git clone ssh://gerrithost:29418/RecipeBook.git RecipeBook
```

```console
> Cloning into RecipeBook...
```

After he clones the repository, he runs a couple of commands to add a Change-Id to his commits. This ID allows Gerrit to link together different versions of the same change being reviewed.

```sh
scp -p -P 29418 gerrithost:hooks/commit-msg RecipeBook/.git/hooks/
chmod u+x .git/hooks/commit-msg
```

Or you can use all-in-one command:

```sh
git clone "ssh://gerrithost:29418/RecipeBook.git RecipeBook" && (cd "RecipeBook" && mkdir -p `git rev-parse --git-dir`/hooks/ && curl -Lo `git rev-parse --git-dir`/hooks/commit-msg https://gerrithost:29418/tools/hooks/commit-msg && chmod +x `git rev-parse --git-dir`/hooks/commit-msg)
```

### Upload a change

The commit must be pushed to a ref in the `refs/for/` namespace which defines the target branch: `refs/for/<target-branch>`

- Without Gerrit: updates the remote branch directly. `git push origin <branch>`
- With Gerrit review workflow: pushes to `refs/for/<branch>` for code review instead of directly updating the branch. `git push origin HEAD:refs/for/<branch>`

**Push for Code Review**

```sh
git commit
git push origin HEAD:refs/for/master
```

**Push with bypassing Code Review**
```sh
git commit
git push origin HEAD:master
```

```console
$ git commit
[master 3cc9e62] Change to a proper, yeast based pizza dough.
 1 file changed, 10 insertions(+), 5 deletions(-)
$ git push origin HEAD:refs/for/master
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 532 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
remote: Processing changes: new: 1, done
remote:
remote: New Changes:
remote:   http://gerrithost/#/c/RecipeBook/+/702 Change to a proper, yeast based pizza dough.
remote:
To ssh://gerrithost:29418/RecipeBook
 * [new branch]      HEAD -> refs/for/master
```

Notice the reference to a `refs/for/master` branch. Gerrit uses this branch to create reviews for the **master** branch. If Max opted to push to a different branch, he would have modified his command to git push origin `HEAD:refs/for/<branch_name>`. When a commit is pushed for review, Gerrit stores it in a staging area which is a branch in the special refs/changes/ namespace. 

### Gerrit Code Review Screen

<img src="assets/images/Gerrit_Code_Review_Screen.png" width=768>


### Reviewing the Change

- Selecting Open from the Changes menu
- Setting up email notifications to stay informed of changes even if you are not added as a reviewer

**Code-Review**: This check requires that someone look at the code and ensures that it meets project guidelines, styles, and other criteria.

**Verified**: This check means that the code actually compiles, passes any unit tests, and performs as expected.

⚠️ The Code-Review and Verified checks require **different permissions** in Gerrit. This requirement allows teams to separate these tasks. For example, an automated process can have the rights to verify a change, but not perform a code review.

> Click the `REPLY` button on the change screen. This allows you to vote on this change.

<img src="assets/images/Reviewing_the_change.png" width=768>

- +2 Looks good to me, approved
- +1 Looks good to me, but someone else must approve
- 0 No score
- -1 I would prefer this is not submitted as is
- -2 This shall not be submitted

⚠️ A change must have at least one +2 vote and no -2 votes before it can be submitted. These numerical values do not accumulate. Two +1 votes do not equate to a +2.

### Reworking the Change



## ❓ Questions 



## References

[Working with Gerrit: An example](https://gerrit-review.googlesource.com/Documentation/intro-gerrit-walkthrough.html)

[User Guide](https://gerrit-review.googlesource.com/Documentation/intro-user.html)

---
