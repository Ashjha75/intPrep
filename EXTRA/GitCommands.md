# Git Commands Cheat Sheet for Interviews

Git is a distributed version control system that helps track changes to code, coordinate work among developers, and maintain project history. This cheat sheet covers the most important Git commands that are commonly asked in interviews and used in day-to-day development.

## Setting Up Git

### Configure Git
```bash
# Set your username
git config --global user.name "Your Name"

# Set your email
git config --global user.email "your.email@example.com"

# Check your configuration
git config --list
```

## Basic Commands

### Initialize a Repository
```bash
# Create a new git repository in current directory
git init
```

### Cloning a Repository
```bash
# Clone a repository from GitHub/GitLab/etc.
git clone <repository-url>

# Clone a specific branch
git clone -b <branch-name> <repository-url>

# Clone with a specific folder name
git clone <repository-url> <folder-name>
```

### Checking Status
```bash
# Check the status of your working directory
git status
```

## Working with Changes

### Staging Changes
```bash
# Add a specific file to staging area
git add <filename>

# Add all changed files to staging area
git add .

# Add files interactively (lets you review each change)
git add -p
```

### Committing Changes
```bash
# Commit staged changes with a message
git commit -m "Your commit message"

# Add and commit all changes in one command
git commit -am "Your commit message"

# Amend the most recent commit (to fix message or add files)
git commit --amend
```

### Viewing Changes
```bash
# View differences between working directory and staging area
git diff

# View differences between staging area and last commit
git diff --staged

# View changes between two commits
git diff <commit1>..<commit2>

# Show changes in a specific file
git diff -- <filename>
```

## Branching and Merging

### Working with Branches
```bash
# List all local branches
git branch

# List all remote branches
git branch -r

# List all branches (local and remote)
git branch -a

# Create a new branch
git branch <branch-name>

# Create and switch to a new branch
git checkout -b <branch-name>

# Switch to an existing branch
git checkout <branch-name>

# Switch to the previous branch
git checkout -
```

### Merging Changes
```bash
# Merge a branch into your current branch
git merge <branch-name>

# Merge with no fast-forward (creates a merge commit)
git merge --no-ff <branch-name>
```

### Handling Conflicts
```bash
# After resolving conflicts manually
git add <resolved-files>
git commit -m "Merge conflict resolution"

# Abort a merge when conflicts occur
git merge --abort
```

### Rebasing
```bash
# Rebase current branch onto another branch
git rebase <branch-name>

# Interactive rebase for the last n commits
git rebase -i HEAD~<n>

# Continue rebase after resolving conflicts
git rebase --continue

# Abort a rebase
git rebase --abort
```

## Remote Operations

### Managing Remotes
```bash
# List all remote repositories
git remote -v

# Add a new remote
git remote add <name> <url>

# Remove a remote
git remote remove <name>

# Change the URL of a remote
git remote set-url <name> <new-url>
```

### Fetching and Pulling
```bash
# Fetch changes from a remote without merging
git fetch <remote>

# Pull changes from remote repository (fetch + merge)
git pull <remote> <branch>

# Pull with rebase instead of merge
git pull --rebase <remote> <branch>
```

### Pushing Changes
```bash
# Push local branch to remote repository
git push <remote> <branch>

# Push local branch to a differently named remote branch
git push <remote> <local-branch>:<remote-branch>

# Force push (use with caution!)
git push --force <remote> <branch>

# Push all local branches to remote
git push --all <remote>
```

## History and Inspection

### Viewing History
```bash
# View commit history
git log

# View compact commit history (one line per commit)
git log --oneline

# View graphical representation of history
git log --graph --oneline --decorate

# View changes in each commit
git log -p

# View history for a specific file
git log -- <filename>
```

### Inspecting Changes
```bash
# Show the details of a specific commit
git show <commit-hash>

# List all files changed in a commit
git show --name-only <commit-hash>
```

## Undoing Changes

### Discarding Changes
```bash
# Discard changes in working directory for a specific file
git checkout -- <filename>

# Discard all changes in working directory
git checkout -- .

# Discard changes to staged files (unstage)
git reset HEAD <filename>
```

### Reverting Commits
```bash
# Create a new commit that undoes changes from a specific commit
git revert <commit-hash>
```

### Resetting
```bash
# Reset to a specific commit (keep changes in working directory)
git reset <commit-hash>

# Reset to a specific commit and discard all changes
git reset --hard <commit-hash>

# Reset the last commit but keep changes staged
git reset --soft HEAD^
```

## Stashing

### Managing Stashes
```bash
# Stash current changes
git stash

# Stash with a message
git stash save "Your stash message"

# List all stashes
git stash list

# Apply the most recent stash without deleting it
git stash apply

# Apply a specific stash
git stash apply stash@{n}

# Apply and remove the most recent stash
git stash pop

# Remove a specific stash
git stash drop stash@{n}
```

## Advanced Commands

### Git Bisect (Finding Bugs)
```bash
# Start a bisect session
git bisect start

# Mark the current commit as bad
git bisect bad

# Mark a specific commit as good
git bisect good <commit-hash>

# End the bisect session
git bisect reset
```

### Cherry-picking
```bash
# Apply a specific commit to current branch
git cherry-pick <commit-hash>

# Apply multiple commits
git cherry-pick <commit-hash1> <commit-hash2>
```

### Submodules
```bash
# Add a submodule
git submodule add <repository-url> <path>

# Initialize submodules in a cloned repository
git submodule init

# Update submodules
git submodule update --recursive
```

### Reflog (Recovery)
```bash
# View reference logs to find lost commits
git reflog

# Recover by creating a branch at a lost commit
git checkout -b <new-branch> <commit-hash>
```

### Tagging
```bash
# Create a lightweight tag
git tag <tag-name>

# Create an annotated tag
git tag -a <tag-name> -m "Tag message"

# List all tags
git tag

# Push tags to remote
git push <remote> --tags
```

## Git Workflows

### Feature Branch Workflow
1. Create a feature branch from main/master
   ```bash
   git checkout -b feature/your-feature main
   ```
2. Make changes and commit
3. Push feature branch to remote
   ```bash
   git push origin feature/your-feature
   ```
4. Create pull request (on GitHub/GitLab)
5. After review, merge to main/master

### Gitflow Workflow
- Main branches: `master`, `develop`
- Supporting branches: `feature`, `release`, `hotfix`

```bash
# Create a feature branch from develop
git checkout -b feature/your-feature develop

# Create a release branch from develop
git checkout -b release/v1.0 develop

# Merge release to master and develop
git checkout master
git merge release/v1.0
git checkout develop
git merge release/v1.0
git branch -d release/v1.0

# Create a hotfix branch from master
git checkout -b hotfix/critical-fix master
```

## Common Interview Questions About Git

1. **What's the difference between `git merge` and `git rebase`?**
   - `git merge` creates a new merge commit that combines changes from both branches
   - `git rebase` reapplies your branch's commits on top of another branch, creating a linear history

2. **How do you resolve merge conflicts in Git?**
   - Edit the conflicted files manually to resolve the conflicts
   - Use `git add` to mark them as resolved
   - Complete the merge with `git commit`

3. **What is `git stash` and when would you use it?**
   - `git stash` temporarily stores your uncommitted changes
   - Use it when you need to switch branches but don't want to commit incomplete work

4. **Explain Git's three-tree architecture.**
   - Working directory: where you make changes
   - Staging area (index): stores changes to be committed
   - Repository: stores committed changes

5. **What's the purpose of Git hooks?**
   - Git hooks are scripts that run automatically before or after Git events
   - They enable custom workflows, validation, and automation

6. **How can you undo a commit?**
   - For local commits: `git reset HEAD~1`
   - For pushed commits: `git revert <commit-hash>`

7. **What is a detached HEAD state?**
   - It occurs when you checkout a specific commit instead of a branch
   - Changes made won't belong to any branch until you create one

8. **How do you squash multiple commits?**
   - Use interactive rebase: `git rebase -i HEAD~n`
   - Mark commits as "squash" or "fixup" in the editor

9. **What is Git blame and how is it useful?**
   - `git blame` shows who modified each line in a file and when
   - Useful for tracking down bugs or understanding code history
