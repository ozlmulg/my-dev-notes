# Git Cheatsheet

## Setup and Configuration

**Set user name:**

`git config --global user.name "yourusername"`

**Set user email:**

`git config --global user.email "youremail@mail.com"`

**Manage Personal Access Token info:**
[GitHub PAT Docs](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)

## Repository Initialization

**Initialize Git repository:**

`git init`

**Clone repository:**

- Via https: `git clone https://github.com/yourusername/yourrepo.git your-new-directory`

- Via ssh: `git clone git@github.com:yourusername/yourrepo.git your-new-directory`

## Staging and Committing

**Check the status of changes:**

`git status`

**Stage a file:**

`git add README.md`

**Stage multiple files:**

`git add README.md index.html`

**Stage all changes (excluding ignored):**

`git add *`

or

`git add .`

**Unstage a file:**

`git rm --cached .vscode`

**Unstage multiple files:**

`git rm --cached -r .vscode/ bin/`

**Unstage all files:**

`git rm -r --cached .`

**Commit with the message:**

`git commit -m "initial commit"`

**Amend last commit with staged changes:**

`git commit --amend --all`

**Change commit message:**

`git commit -am "updated commit" --amend`

## Branching and Merging

**List branches:**

`git branch`

**Create new branch:**

`git branch feature-restructure`

**Create the new branch and switch to it:**

`git checkout -b feature-restructure`

**Delete branch:**

`git branch -D feature-restructure`

**Rename branch:**

`git branch -m feature-new-name`

**Switch branch:**

`git checkout $branch_name`

**Merge branch into current branch:**

`git merge feature-restructure`

## Working with Commits

**View commit history:**

`git log`

**Checkout specific commit:**

`git checkout $commit_id`

**Hard reset to commit (WARNING: discards changes):**

`git reset --hard $commit_id`

**Soft reset to commit:**

`git reset --soft HEAD~`

**Unstage file with restore:**

`git restore --staged index.html`

**Discard changes in working directory:**

`git restore index.html`

## Remote Repositories

**Add remote URL:**

`git remote add origin https://github.com/yourusername/yourrepo.git`

**Remove remote URL:**

`git remote remove origin`

**Change remote URL:**

`git remote set-url origin https://github.com/yourusername/yourrepo.git`

**Show remote URL:**

`git remote get-url origin`

## Pushing and Pulling

**Push to remote branch:**

`git push origin master`

**Push and set upstream:**

`git push --set-upstream origin main`

**Push after the upstream set:**

`git push`

**Pull with rebase:**

`git pull --rebase origin master`

**Set upstream branch for current branch:**

`git branch --set-upstream-to=origin/master`

**Pull after the upstream set:**

`git pull`

## Miscellaneous

- Skip CI workflow by adding `[skip ci]` in the commit message

  Add `[skip ci]` anywhere in your git commit message to skip CI workflows.

