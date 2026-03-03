# Git & GitHub Tutorial

Resource: [Git & GitHub Tutorial | Visualized Git Course for Beginner & Professional Developers in 2024](https://www.youtube.com/watch?v=S7XpTAnSDL4&list=LL&index=10)

Content:

1. [Basic Git Commands](#basic-git-commands)
2. [Pull Request](#pull-request)
3. [Resolving Merge Conflicts](#resolving-merge-conflicts)
4. [Removing commit traces - Reset](#removing-commit-traces)
5. [Removing tracing paths](#removing-tracing-paths)
6. [Reverting Changes](#reverting-changes)
7. [Stashing Changes](#stashing-changes)
8. [How to use private Github Repo with SSH](#how-to-use-private-github-repo-with-ssh)
9. [Fetch all remote branches into workspace](#fetch-all-remote-branches-into-workspace)
10. [Change Remote URL of a GitHub Repository](#change-remote-url-of-a-github-repository)
11. [Share GitHub Repo with Rules for a Developer](#share-github-repo-with-rules-for-a-developer)
12. [Update Committer Config](#update-committer-config)
13. [GitHub Search Qualifiers](#github-search-qualifiers)

## Basic Git Commands

### Get Git version
```bash
git --version
```

### Config Username and User E-Mail
```bash
git config --global user.name 'Ata-Pab'
git config --global user.email 'atalaypab@gmail.com'
```

### To know credentials
```bash
git config user.name
git config user.email
```

### Initialize a repository
```bash
git init 
```

### Tracking changes
```bash
git status
```

### Add some files and all files to commit tree
```bash
git add readme.md
git add .
```

### Commit changes
```bash
git commit -m 'readme.md file has been added'
```

### Get Git History
```bash
git log
```

### Restoring previous versions of the code
- Log commits
```bash
git log
```
- Copy a commit "hash" (key)  (like f284e0f...), then
```bash
git checkout f284e0f...
```
You are in "detached HEAD" state (moved HEAD to another commit)
- Return back to the main/master state
```bash
git checkout master
git checkout main
```

### Repository Types

- **Local Repository**: On local machine 
- **Remote Repository**: On Cloud env like GitHub

### Main branch
```bash
git branch -M main
```

### Link Main/Origin Remote Branch to the Cloud repository
```bash
git remote add origin https://github.com/Ata-pab/git_tutorials.git
```
OR you can give it another name (this name actually refers to the remote repo name)

```bash
git remote add my_origin https://github.com/Ata-pab/git_tutorials.git
```

### Push/Syncronize remote branches with Cloud
```bash
git push -u origin main
```

### Add new branch to the repository
```bash
git branch new_branch
```
- To move into the new branch
```bash
git checkout new_branch
```

### Add new branch to the repository and move into it immediately
```bash
git checkout -b new_branch
```

### Add new branch from another branch
```bash
git branch new_branch source_branch
```

### Push new branch to the remote repository
```bash
git push --set-upstream origin new_branch
git push -u origin new_branch
```

### Update Local branch (Fetch changes from Cloud repo)
```bash
git pull
```

### Pull Request
Pull request lets you share your changes with your team for review/feedback
- Click ```Compare & pull request``` button in GitHub repo or use ```Pull requests``` section in the project repo (GitHub top-nav-bar).
- After merging a branch with master/main properly with no conflicts, you can delete the branch.
- If the merge operation happens in the remote repository (GitHub) you need to fetch changes for main/master branch from cloud. 
```bash
git pull origin main
git pull
```

### Working with git flow
1. Clone the repository
2. Create a new branch from the ```main``` branch 
3. Make changes
4. Push the branch to the remote repository
5. Open a Pull Request
6. Merge the changes (on remote or on locale)
7. Pull the merged changes into your local ```main``` branch (if the merge operation happens in remote)
8. Return to step2 for new changes


### Resolving Merge Conflicts
  
If two collaborators work on different branches (```branch1``` and ```branch2```) and make some changes in the **same file** (like ```readme.md```):
- Changes in ```branch1``` have been committed and merged with ```master``` branch after pull & request
- Changes in ```branch2``` have been committed and sent a pull & request to merge changes with ```master``` branch
- After clicking the "Create pull request" on GitHub you will get a **conflict message** like "This branch has conflicts that must be resolved - Merging is blocked".

#### Resolving Flow
1. Checkout to the ```master/main``` branch
```bash
git checkout main
```
2. Pull the latest changes from the remote ```master/main``` branch (After that, your local ```main``` branch and remote ```main``` branch are identical)
```bash
git pull origin main
```
3. Move again to your branch
```bash
git checkout branch2
```
4. Merge the main branch into your branch to identify the problem 
```bash
git merge main
```
5. You probably see "*Auto-merging ... CONFLICT (content): Merge conflict in readme.md. Automatic merge failed; ...*" message. If you use git supported editor like VSCode you just click the conflic file and select from options "**Accept Current Change**", "**Accept Incoming Change**", "**Accept Both Changes**", "**Compare Changes**". (Or you can use Merge Editor in VSCode)
6. Commit your changes after fix the conflict
```bash
git add .
git commit -m 'Resolve merge conflicts'
git push
```
7. Return back to GitHub and check the previous pull & request section. You will see "This branch has no conflicts with the base branch...". Click *Merge pull request*.

#### Conflict Editor Keywords
```<<<<<< HEAD```: Refer to the changes coming from your branch (```branch2```)
```>>>>>> main```: Refer to the changes coming from ```main``` branch

### Removing commit traces
```bash
git reset 
```
It allows to remove the traces of commits (in log (history) - commit messages) but gives us the changes in those to be discarded commits, so we can decide what to do. You can combine all these changes and commit with better comments or delete them entirely.

#### Soft Reset
Keeps changes as unstaged but not deleted. (Add the commit hash to the end of the command)
```bash
git reset --soft f284e0f...
```

#### Mixed Reset
Default reset command (No need to pass any flag)
```bash
git reset f284e0f...
```
It moves the specified commit in the history, unstages the changes and keeps them in the working directory. After you can manually set them to staging state with ```git add .```

#### Hard Reset
It moves to the specified commit in history and discards all changes in the working directory and staging area. All changes are discarded entirely and you won't see any trace at all.
```bash
git reset --hard f284e0f...
```

### Removing tracing paths
If you want to add all content of a folder (like "Debug/") to the .gitignore file to ignore changes in these files but you have committed these changes before, you should clean the cache.
```bash
git rm -r —cached Debug/
```

### Reverting Changes
```bash
git revert f284e0f...
```
It provides to revert project to a specified comment (Undo effect) without losing the commit history - Clear record of changes what you did. <br/>
You will probably encounter conflicts, make changes and finalize files, and commit. Because it tries to merge changes directly from specified commit to the HEAD commit. Add files to the staging with ```git add .``` then continue to revert:
```bash
git revert --continue
```

### Stashing Changes

It let's you temporarily save your uncommitted changes both **staged** and **unstaged** without actually committed them. (It is actually used to make urgent changes, divides normal working process)
```bash
git stash
```
Output will be like: 
```
Saved working directory and index state WIP on ata-pab: 217cd09 Revert "commit messages..."
```
You can make urgent changes before refetch your stashed changes, commit and push them to the remote repo. After all urgent process is done you can stash your changes (fetch again). First find the stage name:
```bash
git stash list
```
Output will be like: 
```
stash@{0}:WIP on ata-pab: 217cd09 Revert "commit messages..."
```

```bash
git stash apply stash@{0}
```

## How to use private Github Repo with SSH

- Open Git bash and set user credentials: 
```bash
git config --global user.name "Your Name"  
git config --global user.email youremail@example.com 
```

- Set Up GitHub Authentication 

You need to authenticate with GitHub to clone a private repository. You can use either **SSH** or **HTTPS**. 

### Using SSH (Recommended) 

- Generate an SSH key (if you don't have one already): 
```bash
ssh-keygen -t rsa -b 4096 -C "atalay-pab@hotmail.com" 
```
Output: 

    Generating public/private rsa key pair. 
    Enter file in which to save the key (/c/Users/atalayp/.ssh/id_rsa): (Enter) 
    Created directory '/c/Users/atalayp/.ssh'. 
    Create password :_

- Copy your public SSH key: 
```bash
cat ~/.ssh/id_rsa.pub 
```
- Add the SSH key to GitHub: 

Go to **GitHub** -> **Settings** -> **SSH and GPG keys** -> **New SSH key**. 

- Paste the public key and give it a title. 
- Open bash again to test connection: 
```bash
ssh -T git@github.com 
```
If successful, you'll see a message like: 

    Hi "your-username"! You've successfully authenticated, but GitHub does not provide shell access.

- In VSCode's terminal (or Git Bash), run: 
```bash
git clone git@github.com:your-username/your-private-repo.git 
```

## Using private Github Repo with SSH on Linux Mint (Ubuntu-based) System

## 1. Check for existing SSH keys

First, open your terminal and check if you already have SSH keys:

```bash
ls -al ~/.ssh
```

You might see files like:

```
id_rsa.pub  id_rsa
```

If you already have keys, you can use them — skip to **Step 3**.
If not, go to the next step.

---

## 2. Generate a new SSH key

Run this command (replace `your_email@example.com` with your GitHub email):

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

> If your system doesn’t support `ed25519`, use:
>
> ```bash
> ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
> ```

When prompted:

* Press **Enter** to accept the default file location.
* Optionally add a **passphrase** for security (recommended).

---

## 3. Start the SSH agent and add your key

Start the agent:

```bash
eval "$(ssh-agent -s)"
```

Add your new key:

```bash
ssh-add ~/.ssh/id_ed25519
```

---

## 4. Add your SSH key to GitHub

Copy your public key to your clipboard:

```bash
cat ~/.ssh/id_ed25519.pub
```

Then:

1. Go to **GitHub → Settings → SSH and GPG keys**
2. Click **“New SSH key”**
3. Give it a name (like “Linux Mint Laptop”)
4. Paste your key
5. Click **Add SSH key**

---

## 5. Test your SSH connection

Run:

```bash
ssh -T git@github.com
```

Expected output:

```
Hi <your-username>! You've successfully authenticated, but GitHub does not provide shell access.
```

If you see this — it’s working 🎉

---

## 6. Clone your private repo using SSH

Instead of HTTPS, clone using SSH:

```bash
git clone git@github.com:username/repo-name.git
```

> You can find this SSH URL on your repo page → “Code” → “SSH” tab.

---

## 7. Make your changes and commit

Navigate to your repo:

```bash
cd repo-name
```

Then:

```bash
git add .
git commit -m "Your commit message"
```

---

## 8. Push your commit

```bash
git push origin main
```

> (Replace `main` with your branch name if different, e.g. `master` or `dev`.)

If it works without asking for a username/password — you’re using SSH successfully.

---

## Optional: Make SSH the default for all GitHub repos

You can globally set GitHub to use SSH:

```bash
git config --global url."git@github.com:".insteadOf "https://github.com/"
```

That way, even if you clone via HTTPS by mistake, Git will automatically use SSH.


## Fetch all remote branches into workspace 
```bash
git config remote.origin.fetch '+refs/heads/*:refs/remotes/origin/*' 
git branch –r 
git fetch 
git branch -a 
git checkout new_branch
```

## Create Tag

### **Step-by-step:**

#### 🏷️ **Create a Tag**

* **Lightweight Tag:**

  ```bash
  git tag v1.0.0
  ```

  > This is like a bookmark to a specific commit. Not recommended for releases.

* **Annotated Tag (Recommended):**

  ```bash
  git tag -a v1.0.0 -m "Release version 1.0.0"
  ```

  > Annotated tags store metadata (tagger name, email, date, message). Ideal for versioning.

#### **Push the Tag to GitHub**

```bash
git push origin v1.0.0
```

#### **Push All Tags (if many were created locally):**

```bash
git push origin --tags
```

If you want to delete a tag:

  ```bash
  git tag -d v1.0.0              # Delete locally
  git push origin :refs/tags/v1.0.0  # Delete from remote
  ```


## Change Remote URL of a GitHub Repository

To change the remote URL of a GitHub repository, you can use the `git remote set-url` command. This is useful if the URL of your repository has changed, or if you want to switch from HTTPS to SSH (or vice versa). You may want to add a remote project which you have worked on to your GitHub account from another location with all its history.

### Check the remotes
First, check the current remote URLs associated with your repository by running:

```bash
git remote -v
```

### Update the remote URL
To change the remote URL, use the following command:

```bash
git remote set-url origin new_url_here
```

### Verify the change
After updating the URL, verify that the change was successful by running:

```bash
git remote -v
```

### Push changes/history to the new remote
If you want to push your local repository's history to the new remote, use:

```bash
git push -u origin --all
```

### Push tags to the new remote
If you have tags that you want to push to the new remote, use:

```bash
git push --tags
```

## Share GitHub Repo with Rules for a Developer

### 1. Add the developer as a collaborator
1. Go to your GitHub repository.
2. Navigate to **Settings** → **Collaborators and teams** → **Add people**.
3. Add the GitHub username of the developer.
4. Set their permission as **"Write"** (this allows them to create branches and submit pull requests but not push directly to protected branches).

---

### 2. Set up branch protection
You need to protect your `main` (or `master`) branch so that:
- The developer cannot push directly to it.
- All changes must go through a **pull request**.
- You must **review and approve** the pull request before merging.

#### Steps:
1. Go to **Settings** → **Branches**.
2. Under **Branch protection rules**, click **"Add branch protection rule."**
3. Set the following options:
   - **Branch name pattern**: Set to `main` or `master` (or your default branch).
   - **Require a pull request before merging**
     - Enable **"Require approvals"** → Set to **1 or more** (this means you need to approve before merging).
   - **Require status checks to pass before merging** (if you have any CI/CD tests).
   - **Include administrators** – if you want to apply the rule to yourself as well.
4. Save the rule.

---

### 3. Workflow for the Developer
1. The developer can:
   - Clone the repo.
   - Create a new branch.
   - Commit and push to that branch.
   - Open a pull request (PR) against the `main` branch.
2. You will receive a notification about the pull request.
3. You can:
   - Review the code.
   - Request changes or approve it.
   - Merge the PR once you’re satisfied.
   - The developer **cannot merge it themselves** unless you give them admin rights.

---

### 4. (Optional) Set Up CODEOWNERS for Auto Reviews
You can create a `CODEOWNERS` file to automatically assign yourself as a reviewer for every pull request:

1. Create a `.github/CODEOWNERS` file in your repository.
2. Add this content:
```plaintext
* @your-github-username
```
This ensures that you're always added as a reviewer for any changes.

---

### 5. (Optional) Use GitHub Actions for Automatic Checks
If you want to automatically check for code quality or tests before merging:
- Set up GitHub Actions to run checks (like linting, tests, etc.).
- Enable **"Require status checks to pass before merging"** in branch protection rules.

---

## Update Committer Config

This part outlines the steps to update the committer configuration for a project repository. You maybe commited changes with different (or invalid) user information, so, you could not push those changes to the remote repository. To fix this, you need to update the committer configuration.

### If all commits are only local (never pushed)

#### Update the Git global/local config

```bash
git config --global user.name "<user_name>"
git config --global user.email "<user_email>"
```

Or only for the current repo:

```bash
git config user.name "<user_name>"
git config user.email "<user_email>"
```

### Rewrite all commits with correct author/committer

If you want to correct **all commits in the branch**, use:

```bash
git filter-branch --env-filter '
export GIT_AUTHOR_EMAIL="atalay.pabuscu@organization.com"
export GIT_COMMITTER_EMAIL="atalay.pabuscu@organization.com"
' -- --all
```

or if you have `git-filter-repo` installed:
```bash
git filter-repo --email-callback '
return b"atalay.pabuscu@organization.com"
'
```

### Rewrite the last N commits with correct author/committer
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

### Check commit author from terminal

```bash
git log --pretty=format:"%h %an <%ae>" -n 25
```

You should see something like:
```
443b859 Atalay Pabuscu <atalay.pabuscu@gmail.com>
...
```

### Clear VSCode Git cache folder if required
```
Ctrl + Shift + P → Git: Clear Git Input History
Ctrl + Shift + P → Git: Refresh
```

---

## GitHub Search Qualifiers

GitHub Code Search supports various search qualifiers to help you refine your search results. Here are some commonly used qualifiers:

### Main Qualifiers

`owner`, `org`, `user`, `repo`, `language`, `filename`, `path`, `type`, `base`, `reason`, `author`, `assignee`, `mentions`, `commenter`, `involves`, `linked`, `status`, `label`, `milestone`, `is`, `archived`, `fork`, `created`, `pushed`, `updated`, `size`, `stars`, `forks`, `topic`, `visibility`, `reviewed-by`, `review-requested`, `draft`, `merged`, `closed`, `state`

### Flex Qualifiers

- `type`: Specifies the type of item to search for.
    - Possible values include `code`, `commit`, `package`, `marketplace`, `issue`, `pr`, `repo`, `user`, etc.
    - Example: `type:code`
- `is`: Filters results based on specific states or conditions.
    - Possible values include `open`, `closed`, `merged`, `unmerged`, `public`, `draft`, `archived`, `fork`, etc.
    - Example: `is:open`
- `in`: Specifies the fields to search within.
    - Possible values include `title`, `body`, `comments`, etc.
    - Example: `in:title`
- `language`: Filters results by programming language.
    - Example: `language:python`
    - You can combine multiple languages using `OR`.
    - Example: `language:python OR language:javascript`
    - You can exclude a language using `-`.
    - Example: `-language:java`
- `created`, `pushed`, `updated`: Filters results based on date ranges.
    - You can use comparison operators like `>`, `<`, `>=`, `<=`.
    - Example: `created:>2022-01-01`
- `size`, `stars`, `forks`: Filters results based on numerical values.
    - You can use comparison operators like `>`, `<`, `>=`, `<=`.
    - Example: `stars:>100`
    - You can combine multiple numerical filters using `AND`.
    - Example: `stars:>100 forks:>50`
- `status`: Filters results based on the status of an item.
    - Possible values include `open`, `closed`, `merged`, etc.
    - Example: `status:open`
- `label`: Filters results based on labels assigned to issues or pull requests.
    - Example: `label:bug`
    - You can combine multiple labels using `AND`.
    - Example: `label:bug label:high-priority`
- `org`, `user`, `owner`: Filters results based on the organization, user, or owner of the repository.
    - Example: `org:github` OR `org:microsoft`
    - Example: `user:octocat`
    - Example: `owner:octocat`

### Sorting Results

You can sort your search results using the `sort` qualifier followed by the sorting criteria. Common sorting options include:

- `sort:stars`: Sorts results by the number of stars.
- `sort:forks`: Sorts results by the number of forks.
- `sort:updated`: Sorts results by the last updated date.
- `sort:created`: Sorts results by the creation date.
- `sort:best-match`: Sorts results by relevance (default).
- `sort:date`: Sorts results by date.
- `sort:date-asc`: Sorts results by date in ascending order.
- `sort:date-desc`: Sorts results by date in descending order.
- Example: `sort:stars`

---
