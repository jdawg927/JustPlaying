# Git & GitHub Learning Plan

Welcome to your Git & GitHub learning journey! This guide will take you from basic branching and pull requests to advanced Git techniques. Each section includes clear explanations, practical commands, hands-on exercises, and best practices.

---

## Table of Contents

1. [Fundamentals](#1-fundamentals)
   - [Creating and Managing Branches](#creating-and-managing-branches)
   - [Making Commits](#making-commits)
   - [Merging Strategies](#merging-strategies)
   - [Handling Merge Conflicts](#handling-merge-conflicts)
2. [Pull Request Workflow](#2-pull-request-workflow)
   - [Creating Pull Requests](#creating-pull-requests)
   - [Code Review Process](#code-review-process)
   - [PR Best Practices](#pr-best-practices)
   - [Merge Options](#merge-options)
3. [Intermediate Features](#3-intermediate-features)
   - [Working with Remotes](#working-with-remotes)
   - [Stashing Changes](#stashing-changes)
   - [Viewing and Navigating History](#viewing-and-navigating-history)
   - [Tagging Releases](#tagging-releases)
   - [Undoing Changes](#undoing-changes)
4. [Advanced Techniques](#4-advanced-techniques)
   - [Interactive Rebasing](#interactive-rebasing)
   - [Cherry-Picking Commits](#cherry-picking-commits)
   - [Git Hooks](#git-hooks)
   - [Submodules and Subtrees](#submodules-and-subtrees)
   - [Bisecting for Bug Hunting](#bisecting-for-bug-hunting)
   - [Reflog for Recovery](#reflog-for-recovery)

---

## 1. Fundamentals

### Creating and Managing Branches

**What are branches?**  
Branches allow you to work on different features or experiments in isolation without affecting the main codebase. Think of them as parallel universes of your code.

**Key Commands:**

```bash
# Create a new branch
git branch feature-name

# Create and switch to a new branch in one command
git checkout -b feature-name
# or using the newer syntax:
git switch -c feature-name

# List all branches
git branch          # local branches
git branch -a       # all branches (local + remote)

# Switch between branches
git checkout branch-name
# or:
git switch branch-name

# Rename a branch
git branch -m old-name new-name

# Delete a branch
git branch -d branch-name       # safe delete (only if merged)
git branch -D branch-name       # force delete
```

**Hands-On Exercise:**

1. Create a new branch called `feature/add-readme`:
   ```bash
   git checkout -b feature/add-readme
   ```

2. Create a simple README.md file:
   ```bash
   echo "# My Learning Repository" > README.md
   ```

3. Check which branch you're on:
   ```bash
   git branch
   ```

4. Switch back to main:
   ```bash
   git checkout main
   ```

5. Notice the README.md is not there on main (it only exists on your feature branch)

**Common Pitfalls:**
- ❌ Making changes on the wrong branch
- ❌ Forgetting which branch you're on
- ✅ Always check `git status` or `git branch` before making changes

---

### Making Commits

**What are commits?**  
Commits are snapshots of your code at a specific point in time. Each commit has a unique ID (hash) and a message describing the changes.

**Key Commands:**

```bash
# Check the status of your working directory
git status

# Add files to staging area
git add filename                # add specific file
git add .                       # add all changes in current directory
git add -A                      # add all changes in repo

# Commit staged changes
git commit -m "Your commit message"

# Add and commit in one step (only for tracked files)
git commit -am "Your commit message"

# View commit history
git log
git log --oneline               # compact view
git log --graph --oneline       # visual branch structure
```

**Writing Good Commit Messages:**

Follow this structure:
```
<type>: <short summary> (50 chars or less)

<optional body with more details>

<optional footer with issue references>
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

Examples:
- ✅ `feat: add user authentication`
- ✅ `fix: resolve null pointer exception in login`
- ✅ `docs: update installation instructions`
- ❌ `updated stuff` (too vague)
- ❌ `Fixed bug` (not descriptive enough)

**Hands-On Exercise:**

1. Switch to your feature branch:
   ```bash
   git checkout feature/add-readme
   ```

2. Stage and commit the README:
   ```bash
   git add README.md
   git commit -m "docs: add initial README with project title"
   ```

3. Make another change to README:
   ```bash
   echo "This is a learning repository for Git and GitHub." >> README.md
   ```

4. Commit this change:
   ```bash
   git add README.md
   git commit -m "docs: add project description to README"
   ```

5. View your commit history:
   ```bash
   git log --oneline
   ```

**Common Pitfalls:**
- ❌ Committing too much in one commit (hard to review and revert)
- ❌ Vague commit messages
- ❌ Committing sensitive information (passwords, API keys)
- ✅ Make small, logical commits with clear messages

---

### Merging Strategies

**What is merging?**  
Merging combines changes from one branch into another. It's how you integrate your feature work back into the main codebase.

**Key Commands:**

```bash
# Merge a branch into your current branch
git merge branch-name

# Merge with a specific strategy
git merge --no-ff branch-name   # always create a merge commit
git merge --ff-only branch-name # only merge if fast-forward possible
git merge --squash branch-name  # squash all commits into one

# Abort a merge if things go wrong
git merge --abort
```

**Merge Strategies Explained:**

1. **Fast-Forward (FF)**: When the target branch hasn't diverged, Git simply moves the pointer forward
   ```
   main:    A---B
                  \
   feature:       C---D
   
   After FF merge:
   main:    A---B---C---D
   ```

2. **No Fast-Forward (--no-ff)**: Always creates a merge commit, preserving branch history
   ```
   main:    A---B-------M
                  \     /
   feature:       C---D
   ```

3. **Squash**: Combines all commits into one before merging
   ```
   main:    A---B---M (contains all changes from C and D)
                  \
   feature:       C---D
   ```

**Hands-On Exercise:**

1. Switch to main branch:
   ```bash
   git checkout main
   ```

2. Merge your feature branch:
   ```bash
   git merge feature/add-readme
   ```

3. If it's a fast-forward merge, you'll see your commits now on main:
   ```bash
   git log --oneline
   ```

4. Create another feature branch for practice:
   ```bash
   git checkout -b feature/add-contributing
   echo "# Contributing Guide" > CONTRIBUTING.md
   git add CONTRIBUTING.md
   git commit -m "docs: add contributing guidelines"
   ```

5. Switch back to main and merge with no-ff:
   ```bash
   git checkout main
   git merge --no-ff feature/add-contributing -m "Merge feature/add-contributing"
   ```

**Common Pitfalls:**
- ❌ Merging into the wrong branch
- ❌ Not testing before merging
- ✅ Always review changes before merging

---

### Handling Merge Conflicts

**What are merge conflicts?**  
Conflicts occur when Git can't automatically merge changes because the same lines were modified in both branches.

**Key Commands:**

```bash
# See which files have conflicts
git status

# After resolving conflicts manually
git add resolved-file
git commit  # completes the merge

# Abort the merge and start over
git merge --abort

# Use a merge tool
git mergetool
```

**Conflict Markers:**
When a conflict occurs, Git adds markers to the file:

```
<<<<<<< HEAD
This is the content from your current branch
=======
This is the content from the branch being merged
>>>>>>> feature-branch
```

**Hands-On Exercise:**

1. Create a conflict intentionally:
   ```bash
   # On main branch
   git checkout main
   echo "Main branch version" > conflict-test.txt
   git add conflict-test.txt
   git commit -m "test: add conflict test on main"
   
   # Create and switch to feature branch
   git checkout -b feature/conflict-test
   echo "Feature branch version" > conflict-test.txt
   git add conflict-test.txt
   git commit -m "test: add conflict test on feature"
   
   # Try to merge - this will create a conflict!
   git checkout main
   git merge feature/conflict-test
   ```

2. Open `conflict-test.txt` and you'll see conflict markers

3. Resolve the conflict by editing the file to keep what you want:
   ```
   Both versions are important!
   Main branch version
   Feature branch version
   ```

4. Complete the merge:
   ```bash
   git add conflict-test.txt
   git commit -m "merge: resolve conflict in conflict-test.txt"
   ```

**Best Practices for Avoiding Conflicts:**
- ✅ Pull frequently from main
- ✅ Keep branches short-lived
- ✅ Communicate with team members
- ✅ Work on different parts of the codebase when possible

---

## 2. Pull Request Workflow

### Creating Pull Requests

**What are Pull Requests (PRs)?**  
Pull Requests are a GitHub feature (similar to Merge Requests in GitLab) that let you propose changes, discuss them, and review code before merging into the main branch.

**Prerequisites:**
- Have a GitHub repository set up
- Push your local branch to GitHub

**Key Commands:**

```bash
# Push your branch to GitHub
git push origin branch-name

# Push and set upstream tracking
git push -u origin branch-name

# Create PR from command line (using GitHub CLI)
gh pr create
gh pr create --title "Title" --body "Description"
```

**Creating a PR on GitHub (Web Interface):**

1. Push your branch:
   ```bash
   git push -u origin feature/add-readme
   ```

2. Go to your repository on GitHub.com

3. You'll see a yellow banner suggesting "Compare & pull request" - click it

4. Fill out the PR form:
   - **Title**: Clear, concise summary
   - **Description**: What changes you made and why
   - **Reviewers**: Who should review (if working with a team)
   - **Labels**: Categorize your PR (bug, enhancement, etc.)
   - **Assignees**: Who's responsible for the PR

5. Click "Create pull request"

**Hands-On Exercise:**

1. Create a new feature branch:
   ```bash
   git checkout -b feature/add-license
   echo "MIT License" > LICENSE
   git add LICENSE
   git commit -m "docs: add MIT license"
   ```

2. Push to GitHub:
   ```bash
   git push -u origin feature/add-license
   ```

3. Go to GitHub and create a PR for this branch

**PR Description Template:**

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
How to test these changes

## Screenshots (if applicable)

## Checklist
- [ ] Code follows project style
- [ ] Tests added/updated
- [ ] Documentation updated
```

---

### Code Review Process

**What is code review?**  
Code review is the process of having teammates examine your code for quality, bugs, and adherence to standards before merging.

**As a PR Author:**

1. **Respond to comments promptly**
2. **Make requested changes**:
   ```bash
   # Make changes on your feature branch
   git add .
   git commit -m "fix: address review comments"
   git push
   ```
3. **Mark conversations as resolved** when addressed
4. **Request re-review** after making changes

**As a Reviewer:**

- Review on GitHub's "Files changed" tab
- Leave comments on specific lines
- Use suggestion feature:
  ```suggestion
  # GitHub will show this as a code suggestion
  improved_code_here()
  ```
- Approve, Request Changes, or Comment
- Be constructive and kind

**Using GitHub CLI for Reviews:**

```bash
# Check out a PR locally
gh pr checkout PR-number

# Review a PR
gh pr review PR-number --approve
gh pr review PR-number --request-changes --body "Comments"
gh pr review PR-number --comment --body "Comments"
```

**Hands-On Exercise:**

1. On GitHub, go to your PR's "Files changed" tab
2. Hover over a line and click the "+" to add a comment
3. Practice approving your own PR (for learning purposes)
4. Merge the PR using the GitHub interface

---

### PR Best Practices

**Size and Scope:**
- ✅ Keep PRs small (< 400 lines of code when possible)
- ✅ One feature or fix per PR
- ✅ Break large features into multiple PRs
- ❌ Don't mix refactoring with new features

**Quality Standards:**
- ✅ All tests pass before requesting review
- ✅ Add tests for new functionality
- ✅ Update documentation
- ✅ Self-review before requesting others' time
- ✅ Resolve linter warnings

**Communication:**
- ✅ Clear title and description
- ✅ Link to relevant issues: "Closes #123"
- ✅ Add screenshots/GIFs for UI changes
- ✅ Explain "why" not just "what"

**Draft PRs:**
Use draft PRs for work-in-progress to get early feedback:
```bash
gh pr create --draft
```

Convert to ready when done:
- Click "Ready for review" on GitHub

---

### Merge Options

**Three Ways to Merge PRs on GitHub:**

1. **Create a Merge Commit** (default)
   - Preserves all commits and branch history
   - Creates a merge commit
   - Best for: Long-lived feature branches, when history is important
   ```
   main:    A---B-------M
                  \     /
   feature:       C---D
   ```

2. **Squash and Merge**
   - Combines all commits into one
   - Creates a clean, linear history
   - Best for: Small features, cleanup of messy commit history
   ```
   main:    A---B---C'
   (where C' contains all changes from C and D)
   ```

3. **Rebase and Merge**
   - Replays commits on top of base branch
   - No merge commit
   - Creates a linear history
   - Best for: Maintaining clean, linear history
   ```
   main:    A---B---C---D
   ```

**Choosing the Right Strategy:**

| Strategy | Pros | Cons | Best For |
|----------|------|------|----------|
| Merge Commit | Complete history preserved | Can get cluttered | Important features, releases |
| Squash | Clean history, easy to revert | Loses individual commits | Small PRs, experimental work |
| Rebase | Linear history, no merge commits | Can be complex | Teams with rebase workflow |

**Hands-On Exercise:**

1. Create three different feature branches:
   ```bash
   git checkout -b feature/test-merge
   echo "test" > merge-test.txt
   git add merge-test.txt
   git commit -m "test: merge commit style"
   git push -u origin feature/test-merge
   ```

2. Create PRs for each and try different merge strategies on GitHub

3. After merging, compare the histories:
   ```bash
   git checkout main
   git pull
   git log --oneline --graph
   ```

---

## 3. Intermediate Features

### Working with Remotes

**What are remotes?**  
Remotes are versions of your repository hosted on the internet or network. `origin` is typically the default remote pointing to GitHub.

**Key Commands:**

```bash
# List remotes
git remote -v

# Add a remote
git remote add name url

# Remove a remote
git remote remove name

# Rename a remote
git remote rename old-name new-name

# Fetch updates from remote (doesn't merge)
git fetch origin

# Fetch and merge (pull)
git pull origin branch-name
git pull  # pulls current branch's upstream

# Push to remote
git push origin branch-name
git push  # pushes current branch to its upstream

# Set upstream for current branch
git push -u origin branch-name

# View remote information
git remote show origin
```

**Understanding Fetch vs Pull:**

- **Fetch**: Downloads changes but doesn't merge them
  ```bash
  git fetch origin
  git log origin/main  # view remote commits
  git merge origin/main  # merge when ready
  ```

- **Pull**: Fetch + Merge in one command
  ```bash
  git pull origin main
  # equivalent to:
  # git fetch origin
  # git merge origin/main
  ```

**Working with Forks:**

```bash
# Add upstream remote (original repo you forked from)
git remote add upstream https://github.com/original-owner/repo.git

# Sync your fork with upstream
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

**Hands-On Exercise:**

1. Check your remotes:
   ```bash
   git remote -v
   ```

2. View remote branches:
   ```bash
   git branch -r
   ```

3. Fetch without merging:
   ```bash
   git fetch origin
   ```

4. See the difference between local and remote:
   ```bash
   git log HEAD..origin/main --oneline
   ```

5. Pull changes:
   ```bash
   git pull
   ```

**Common Pitfalls:**
- ❌ Force pushing (`git push -f`) on shared branches
- ❌ Not pulling before starting work
- ✅ Always pull before pushing
- ✅ Communicate before force pushing

---

### Stashing Changes

**What is stashing?**  
Stashing temporarily saves your uncommitted changes so you can switch branches or pull updates without committing incomplete work.

**Key Commands:**

```bash
# Stash current changes
git stash
git stash save "description of changes"

# List all stashes
git stash list

# Apply most recent stash (keeps stash)
git stash apply

# Apply and remove most recent stash
git stash pop

# Apply specific stash
git stash apply stash@{2}

# Create a branch from a stash
git stash branch branch-name stash@{0}

# Clear all stashes
git stash clear

# Drop a specific stash
git stash drop stash@{0}

# Stash including untracked files
git stash -u

# Stash with interactive mode
git stash -p
```

**Hands-On Exercise:**

1. Make some changes without committing:
   ```bash
   echo "Temporary work" > temp.txt
   ```

2. Try to switch branches (Git will warn you):
   ```bash
   git checkout -b another-branch
   ```

3. Stash the changes:
   ```bash
   git stash save "temp work in progress"
   ```

4. Now switch branches:
   ```bash
   git checkout another-branch
   # do some work
   git checkout main
   ```

5. Restore your stashed work:
   ```bash
   git stash pop
   ```

6. View stash contents without applying:
   ```bash
   git stash show -p stash@{0}
   ```

**Common Use Cases:**
- 🔄 Switching branches with uncommitted changes
- 🔄 Pulling updates into a dirty working directory
- 🔄 Temporarily setting aside work to fix a bug
- 🔄 Testing if changes are causing an issue

---

### Viewing and Navigating History

**Key Commands:**

```bash
# Basic log
git log
git log --oneline              # compact view
git log --graph --oneline      # visual branch structure
git log --all --decorate       # all branches with decorations

# Limit output
git log -n 5                   # last 5 commits
git log --since="2 weeks ago"
git log --until="2024-01-01"
git log --author="Name"

# Search commits
git log --grep="keyword"       # search commit messages
git log -S "function_name"     # search for code changes (pickaxe)
git log -G "regex"             # search with regex

# View changes
git log -p                     # show patches (diffs)
git log --stat                 # show stats (files changed)

# View specific file history
git log -- filename
git log -p -- filename         # with changes

# View who changed each line
git blame filename
git blame -L 10,20 filename    # specific lines

# Show a specific commit
git show commit-hash
git show HEAD                  # most recent commit
git show HEAD~3                # 3 commits ago

# Compare commits
git diff commit1 commit2
git diff HEAD~3 HEAD
git diff main feature-branch

# Find when a bug was introduced
git bisect start
git bisect bad                 # current version is bad
git bisect good commit-hash    # known good version
# Git will check out commits for you to test
git bisect good/bad            # mark each
git bisect reset               # when done
```

**Useful Log Formats:**

```bash
# Pretty format
git log --pretty=format:"%h - %an, %ar : %s"

# Graph with details
git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit

# Files changed
git log --name-status
```

**Hands-On Exercise:**

1. View your repository history:
   ```bash
   git log --oneline --graph --all
   ```

2. Search for commits mentioning "README":
   ```bash
   git log --grep="README"
   ```

3. See who last modified each line of README:
   ```bash
   git blame README.md
   ```

4. View changes in the last 3 commits:
   ```bash
   git log -p -3
   ```

5. Compare your current branch with main:
   ```bash
   git diff main..HEAD
   ```

**Pro Tips:**
- 💡 Create git aliases for complex commands
- 💡 Use `git log --follow filename` to track file renames
- 💡 `gitk` or `git gui` for visual history browsing

---

### Tagging Releases

**What are tags?**  
Tags mark specific points in history as important, typically used for releases (v1.0, v2.0, etc.).

**Key Commands:**

```bash
# Create a lightweight tag
git tag v1.0

# Create an annotated tag (recommended for releases)
git tag -a v1.0 -m "Version 1.0 release"

# List tags
git tag
git tag -l "v1.*"              # filter tags

# Show tag information
git show v1.0

# Tag a specific commit
git tag -a v1.0 commit-hash -m "Message"

# Push tags to remote
git push origin v1.0           # single tag
git push origin --tags         # all tags

# Delete a tag
git tag -d v1.0                # local
git push origin --delete v1.0  # remote

# Checkout a tag
git checkout v1.0
git checkout -b branch-name v1.0  # create branch from tag
```

**Semantic Versioning:**

Follow the format: `MAJOR.MINOR.PATCH`

- **MAJOR**: Breaking changes
- **MINOR**: New features (backward compatible)
- **PATCH**: Bug fixes

Examples: `v1.0.0`, `v1.2.3`, `v2.0.0`

**Hands-On Exercise:**

1. Create your first release tag:
   ```bash
   git tag -a v0.1.0 -m "Initial learning repository setup"
   ```

2. List all tags:
   ```bash
   git tag
   ```

3. View tag details:
   ```bash
   git show v0.1.0
   ```

4. Push tag to GitHub:
   ```bash
   git push origin v0.1.0
   ```

5. On GitHub, go to "Releases" and you'll see your tag

**Creating GitHub Releases:**

1. Go to your repo on GitHub
2. Click "Releases" → "Create a new release"
3. Select your tag or create a new one
4. Add release notes
5. Attach binaries if needed
6. Publish release

**Common Pitfalls:**
- ❌ Using lightweight tags for releases
- ❌ Forgetting to push tags
- ✅ Use annotated tags with `-a` for releases
- ✅ Follow semantic versioning

---

### Undoing Changes

**The Three Trees of Git:**
1. **Working Directory**: Your actual files
2. **Staging Area (Index)**: Prepared for next commit
3. **Repository (HEAD)**: Last commit

**Key Commands:**

```bash
# Undo changes in working directory (destructive!)
git restore filename
git checkout -- filename       # older syntax

# Unstage files (keep changes in working directory)
git restore --staged filename
git reset HEAD filename        # older syntax

# Amend the last commit
git commit --amend
git commit --amend --no-edit   # keep same message

# Reset commits (use with caution!)
git reset --soft HEAD~1        # undo commit, keep changes staged
git reset --mixed HEAD~1       # undo commit, unstage changes (default)
git reset --hard HEAD~1        # undo commit, discard changes (destructive!)

# Revert a commit (creates new commit)
git revert commit-hash

# Discard all local changes
git reset --hard HEAD
git clean -fd                  # remove untracked files and directories
```

**Reset Modes Explained:**

| Mode | Commit | Staging | Working Dir |
|------|--------|---------|-------------|
| `--soft` | Undo | Keep | Keep |
| `--mixed` | Undo | Undo | Keep |
| `--hard` | Undo | Undo | Undo |

**Revert vs Reset:**

- **Reset**: Rewrites history (dangerous on shared branches)
  ```bash
  git reset --hard HEAD~1
  ```

- **Revert**: Creates a new commit that undoes changes (safe for shared branches)
  ```bash
  git revert commit-hash
  ```

**Hands-On Exercise:**

1. Make a change and stage it:
   ```bash
   echo "mistake" > oops.txt
   git add oops.txt
   ```

2. Unstage it:
   ```bash
   git restore --staged oops.txt
   ```

3. Discard the change:
   ```bash
   git restore oops.txt
   ```

4. Make a commit you want to undo:
   ```bash
   echo "wrong" > wrong.txt
   git add wrong.txt
   git commit -m "wrong commit"
   ```

5. Undo the commit but keep changes:
   ```bash
   git reset --soft HEAD~1
   ```

6. Make a commit and revert it:
   ```bash
   git commit -m "test commit"
   git revert HEAD
   ```

**Common Pitfalls:**
- ❌ Using `reset --hard` on shared branches
- ❌ Forgetting that reset rewrites history
- ✅ Use `revert` for shared branches
- ✅ Test destructive commands on a test branch first

---

## 4. Advanced Techniques

### Interactive Rebasing

**What is rebasing?**  
Rebasing rewrites commit history by applying commits from one branch onto another. It creates a cleaner, linear history.

**Key Commands:**

```bash
# Rebase current branch onto another
git rebase main

# Interactive rebase (most powerful)
git rebase -i HEAD~5           # edit last 5 commits
git rebase -i commit-hash      # rebase from specific commit

# Continue after resolving conflicts
git rebase --continue

# Skip a commit during rebase
git rebase --skip

# Abort rebase
git rebase --abort

# Rebase without changing commit dates
git rebase --committer-date-is-author-date
```

**Interactive Rebase Commands:**

When you run `git rebase -i`, you'll see an editor with commits. You can use these commands:

- `pick` (p): Use commit as-is
- `reword` (r): Change commit message
- `edit` (e): Stop to edit commit
- `squash` (s): Combine with previous commit
- `fixup` (f): Like squash but discard message
- `drop` (d): Remove commit
- `exec` (x): Run shell command

**Example:**
```
pick abc1234 feat: add login
squash def5678 fix: typo in login
reword ghi9012 feat: add logout
drop jkl3456 test: temporary test
```

**Hands-On Exercise:**

1. Create a branch with multiple commits:
   ```bash
   git checkout -b feature/rebase-practice
   echo "step 1" > steps.txt
   git add steps.txt
   git commit -m "step 1"
   
   echo "step 2" >> steps.txt
   git commit -am "step 2"
   
   echo "step 3" >> steps.txt
   git commit -am "step 3"
   
   echo "typo fix" >> steps.txt
   git commit -am "oops typo"
   ```

2. Interactive rebase to clean up:
   ```bash
   git rebase -i HEAD~4
   ```

3. In the editor, squash the "oops typo" commit into step 3:
   ```
   pick abc1234 step 1
   pick def5678 step 2
   pick ghi9012 step 3
   squash jkl3456 oops typo
   ```

4. Save and close, then edit the combined commit message

**Rebasing a Feature Branch:**

```bash
# Update your feature branch with latest main
git checkout feature-branch
git rebase main

# If conflicts occur:
# 1. Fix conflicts in files
# 2. git add resolved-files
# 3. git rebase --continue
```

**Common Pitfalls:**
- ❌ Rebasing public/shared branches (rewrites history!)
- ❌ Rebasing instead of merging for important historical context
- ✅ Only rebase local branches or private feature branches
- ✅ Use `git pull --rebase` to maintain linear history

**Golden Rule:**
> Never rebase commits that have been pushed to a shared repository unless you're absolutely sure no one else is using them.

---

### Cherry-Picking Commits

**What is cherry-picking?**  
Cherry-picking applies specific commits from one branch to another. Useful for selectively pulling in changes.

**Key Commands:**

```bash
# Cherry-pick a single commit
git cherry-pick commit-hash

# Cherry-pick multiple commits
git cherry-pick commit1 commit2 commit3

# Cherry-pick a range
git cherry-pick start-commit^..end-commit

# Cherry-pick without committing (stage only)
git cherry-pick -n commit-hash
git cherry-pick --no-commit commit-hash

# Continue after resolving conflicts
git cherry-pick --continue

# Abort cherry-pick
git cherry-pick --abort

# Cherry-pick and edit message
git cherry-pick -e commit-hash

# Cherry-pick and sign-off
git cherry-pick -s commit-hash
```

**Hands-On Exercise:**

1. Create two branches with different commits:
   ```bash
   # Branch A
   git checkout -b branch-a
   echo "Feature A1" > feature-a.txt
   git add feature-a.txt
   git commit -m "feat: add feature A1"
   
   echo "Feature A2" >> feature-a.txt
   git commit -am "feat: add feature A2"
   
   # Branch B
   git checkout -b branch-b main
   echo "Feature B" > feature-b.txt
   git add feature-b.txt
   git commit -m "feat: add feature B"
   ```

2. Cherry-pick a specific commit from branch-a:
   ```bash
   git log branch-a --oneline  # find the commit hash for A1
   git cherry-pick <commit-hash-of-A1>
   ```

3. Now branch-b has Feature B and Feature A1, but not A2

**Use Cases:**
- 🍒 Backporting bug fixes to release branches
- 🍒 Applying hotfixes to multiple branches
- 🍒 Selectively bringing in features
- 🍒 Recovering commits from deleted branches

**Common Pitfalls:**
- ❌ Cherry-picking creates duplicate commits (different hashes)
- ❌ Overusing cherry-pick instead of proper merging
- ✅ Document which commits were cherry-picked
- ✅ Consider merging instead if you need many commits

---

### Git Hooks

**What are Git hooks?**  
Git hooks are scripts that run automatically at specific points in the Git workflow. They enforce standards and automate tasks.

**Hook Types:**

**Client-side hooks:**
- `pre-commit`: Before commit is created
- `prepare-commit-msg`: Before commit message editor opens
- `commit-msg`: After commit message is provided
- `post-commit`: After commit is created
- `pre-push`: Before push to remote
- `pre-rebase`: Before rebase
- `post-checkout`: After checkout
- `post-merge`: After merge

**Server-side hooks:**
- `pre-receive`: Before accepting pushed refs
- `update`: Like pre-receive but runs per branch
- `post-receive`: After push is accepted

**Setting Up Hooks:**

Hooks live in `.git/hooks/` and must be executable.

```bash
# Navigate to hooks directory
cd .git/hooks

# Create a pre-commit hook
cat > pre-commit << 'EOF'
#!/bin/bash

echo "Running pre-commit hook..."

# Check for console.log statements (example)
if git diff --cached --name-only | grep -E '\.(js|ts)$' | xargs grep -n 'console.log'; then
    echo "Error: Found console.log statements. Please remove them."
    exit 1
fi

exit 0
EOF

# Make it executable
chmod +x pre-commit
```

**Hands-On Exercise:**

1. Create a simple pre-commit hook:
   ```bash
   cat > .git/hooks/pre-commit << 'EOF'
   #!/bin/bash
   
   echo "🔍 Running pre-commit checks..."
   
   # Check for TODO comments
   if git diff --cached | grep -i "TODO"; then
       echo "⚠️  Warning: Found TODO comments in staged changes"
       echo "Continue anyway? (y/n)"
       read answer
       if [ "$answer" != "y" ]; then
           exit 1
       fi
   fi
   
   echo "✅ Pre-commit checks passed!"
   exit 0
   EOF
   
   chmod +x .git/hooks/pre-commit
   ```

2. Test it:
   ```bash
   echo "// TODO: fix this" > test.js
   git add test.js
   git commit -m "test commit"
   # Your hook will run!
   ```

**Commit Message Hook Example:**

```bash
# .git/hooks/commit-msg
#!/bin/bash

commit_msg_file=$1
commit_msg=$(cat "$commit_msg_file")

# Check if commit message matches pattern
if ! echo "$commit_msg" | grep -qE "^(feat|fix|docs|style|refactor|test|chore): .+"; then
    echo "❌ Commit message must follow format: <type>: <description>"
    echo "Types: feat, fix, docs, style, refactor, test, chore"
    exit 1
fi

exit 0
```

**Sharing Hooks with Team:**

Git hooks in `.git/hooks` aren't version controlled. To share:

1. Create a `scripts/hooks/` directory in your repo:
   ```bash
   mkdir -p scripts/hooks
   ```

2. Store hooks there and document setup:
   ```bash
   cp .git/hooks/pre-commit scripts/hooks/
   ```

3. Team members run:
   ```bash
   ln -s ../../scripts/hooks/pre-commit .git/hooks/pre-commit
   ```

**Popular Hook Managers:**
- **Husky**: For Node.js projects
- **pre-commit**: Python-based framework
- **lefthook**: Fast parallel hook runner

**Use Cases:**
- ✅ Enforce coding standards
- ✅ Run linters and formatters
- ✅ Prevent committing secrets
- ✅ Run tests before push
- ✅ Validate commit messages

---

### Submodules and Subtrees

**What are submodules and subtrees?**  
Both allow you to include external Git repositories within your repository, but they work differently.

#### Git Submodules

**Key Commands:**

```bash
# Add a submodule
git submodule add https://github.com/user/repo.git path/to/submodule

# Initialize submodules after cloning
git submodule init
git submodule update

# Clone a repo with submodules
git clone --recursive https://github.com/user/repo.git

# Update all submodules to latest
git submodule update --remote

# Execute command in each submodule
git submodule foreach 'git pull origin main'

# Remove a submodule
git submodule deinit path/to/submodule
git rm path/to/submodule
rm -rf .git/modules/path/to/submodule
```

**Hands-On Exercise (Submodules):**

1. Add a submodule:
   ```bash
   git submodule add https://github.com/github/gitignore.git external/gitignore
   git commit -m "chore: add gitignore templates as submodule"
   ```

2. View submodule status:
   ```bash
   git submodule status
   ```

3. Update the submodule:
   ```bash
   cd external/gitignore
   git pull origin main
   cd ../..
   git add external/gitignore
   git commit -m "chore: update gitignore submodule"
   ```

#### Git Subtrees

**Key Commands:**

```bash
# Add a subtree
git subtree add --prefix path/to/subtree https://github.com/user/repo.git main --squash

# Pull updates from subtree
git subtree pull --prefix path/to/subtree https://github.com/user/repo.git main --squash

# Push changes back to subtree repo
git subtree push --prefix path/to/subtree https://github.com/user/repo.git main

# Split out a subtree into a separate repo
git subtree split --prefix path/to/subtree -b new-branch
```

**Submodules vs Subtrees:**

| Feature | Submodules | Subtrees |
|---------|-----------|----------|
| Complexity | Higher | Lower |
| Nested repos | Yes | No (flattened) |
| Cloning | Needs `--recursive` | Works normally |
| Updating | Manual | Simple pull |
| Committing | Separate repos | Mixed in main repo |
| Best for | Well-defined dependencies | Vendor code, libraries |

**Hands-On Exercise (Subtrees):**

1. Add a subtree:
   ```bash
   git subtree add --prefix vendor/library https://github.com/user/library.git main --squash
   ```

2. Pull updates:
   ```bash
   git subtree pull --prefix vendor/library https://github.com/user/library.git main --squash
   ```

**Use Cases:**
- 📦 Including third-party libraries
- 📦 Sharing code between projects
- 📦 Monorepo architectures
- 📦 Vendoring dependencies

---

### Bisecting for Bug Hunting

**What is git bisect?**  
Git bisect uses binary search to find the commit that introduced a bug. It's incredibly powerful for debugging.

**Key Commands:**

```bash
# Start bisecting
git bisect start

# Mark current state as bad
git bisect bad

# Mark a known good commit
git bisect good commit-hash

# Git will checkout a commit in the middle
# Test if bug is present, then mark it:
git bisect good   # if bug not present
git bisect bad    # if bug is present

# Continue until Git finds the culprit
# When done:
git bisect reset

# Automate bisecting with a test script
git bisect start HEAD v1.0
git bisect run test-script.sh

# Skip commits that can't be tested
git bisect skip

# Visualize bisect
git bisect visualize
```

**Hands-On Exercise:**

1. Create a history with a "bug":
   ```bash
   git checkout -b bisect-demo
   
   # Commit 1 (good)
   echo "function works() { return true; }" > code.js
   git add code.js
   git commit -m "Initial working version"
   
   # Commit 2 (good)
   echo "function works() { return true; } // added comment" > code.js
   git commit -am "Add comment"
   
   # Commit 3 (bad - introduces bug)
   echo "function works() { return false; }" > code.js
   git commit -am "Refactor code"
   
   # Commit 4 (bad)
   echo "function works() { return false; } // updated" > code.js
   git commit -am "Update code"
   ```

2. Start bisecting:
   ```bash
   git bisect start
   git bisect bad HEAD         # current is bad
   git bisect good HEAD~3      # first commit was good
   ```

3. Git checks out a commit - check the code:
   ```bash
   cat code.js
   ```

4. Mark it good or bad:
   ```bash
   git bisect good  # or bad
   ```

5. Continue until Git identifies the problematic commit

6. Reset:
   ```bash
   git bisect reset
   ```

**Automated Bisecting:**

Create a test script (`test.sh`):
```bash
#!/bin/bash
# Exit 0 if good, 1 if bad

grep "return true" code.js
```

Run automated bisect:
```bash
chmod +x test.sh
git bisect start HEAD HEAD~3
git bisect run ./test.sh
```

**Use Cases:**
- 🐛 Finding when a bug was introduced
- 🐛 Identifying breaking changes
- 🐛 Debugging performance regressions
- 🐛 When you know it worked before but not sure when it broke

---

### Reflog for Recovery

**What is reflog?**  
Reflog (reference log) records every change to branch tips and HEAD. It's your safety net for recovering "lost" commits.

**Key Commands:**

```bash
# View reflog
git reflog
git reflog show branch-name

# View reflog with dates
git reflog --date=iso

# Find lost commits
git reflog | grep "commit message"

# Recover a lost commit
git cherry-pick commit-hash
git checkout commit-hash
git branch recovery-branch commit-hash

# Reset to a previous state
git reset --hard HEAD@{2}

# View reflog for specific branch
git reflog show feature-branch

# Expire old reflog entries (careful!)
git reflog expire --expire=30.days refs/heads/main
```

**Understanding Reflog Output:**

```
abc1234 HEAD@{0}: commit: latest commit
def5678 HEAD@{1}: commit: previous commit  
ghi9012 HEAD@{2}: reset: moving to HEAD~1
jkl3456 HEAD@{3}: commit: commit before reset
```

**Hands-On Exercise:**

1. Create and "lose" a commit:
   ```bash
   git checkout -b recovery-test
   echo "important data" > important.txt
   git add important.txt
   git commit -m "important commit"
   
   # Note the commit hash
   git log --oneline -1
   
   # "Lose" it with reset
   git reset --hard HEAD~1
   
   # File is gone!
   ls important.txt  # file not found
   ```

2. Find it in reflog:
   ```bash
   git reflog
   # Look for "important commit"
   ```

3. Recover it:
   ```bash
   git cherry-pick HEAD@{1}
   # Or create a branch:
   git branch recovery HEAD@{1}
   ```

**Recovery Scenarios:**

**1. Accidental reset --hard:**
```bash
git reset --hard HEAD~5  # oops, went too far!
git reflog
git reset --hard HEAD@{1}  # recover
```

**2. Deleted branch:**
```bash
git branch -D feature-branch  # oops!
git reflog | grep feature-branch
git checkout -b feature-branch commit-hash
```

**3. Bad rebase:**
```bash
git rebase -i HEAD~10  # messed up!
git reflog
git reset --hard HEAD@{1}  # before rebase
```

**4. Amended commit:**
```bash
git commit --amend  # oops, wanted original
git reflog
git reset --soft HEAD@{1}
```

**Important Notes:**
- 📝 Reflog is local only (not pushed to remote)
- 📝 Reflog entries expire (default: 90 days for reachable, 30 for unreachable)
- 📝 Reflog doesn't track unstaged/uncommitted changes
- 📝 Use reflog as your "undo" for Git operations

**Pro Tips:**
- 💡 Check reflog before panicking about lost work
- 💡 Reflog saved by commit, reset, checkout, rebase, merge
- 💡 `git fsck --lost-found` can find even more orphaned commits

---

## Best Practices Summary

### General Workflow

1. **Commit Often**: Small, logical commits are easier to review and revert
2. **Write Clear Messages**: Future you (and teammates) will thank you
3. **Pull Before Push**: Avoid conflicts by staying up-to-date
4. **Branch Strategy**: Use branches for all changes, keep main stable
5. **Test Before Committing**: Don't commit broken code
6. **Review Your Changes**: `git diff` before committing

### Branch Naming Conventions

```
feature/description    - New features
bugfix/description     - Bug fixes
hotfix/description     - Urgent fixes
refactor/description   - Code refactoring
docs/description       - Documentation
test/description       - Tests only
```

### Commit Message Template

```
<type>(<scope>): <subject>

<body>

<footer>
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`

### When to Use What

| Task | Command/Strategy |
|------|-----------------|
| Experiment safely | Create a branch |
| Save incomplete work | `git stash` |
| Combine multiple commits | `git rebase -i` or squash merge |
| Fix wrong commit message | `git commit --amend` |
| Undo public commit | `git revert` |
| Undo local commit | `git reset` |
| Find when bug introduced | `git bisect` |
| Recover lost work | `git reflog` |
| Share code between repos | Submodules or subtrees |

---

## Common Scenarios and Solutions

### "I committed to the wrong branch!"

```bash
# If you haven't pushed:
git reset HEAD~ --soft
git stash
git checkout correct-branch
git stash pop
git add .
git commit -m "Your message"
```

### "I need to update my PR with latest main"

```bash
git checkout feature-branch
git fetch origin
git rebase origin/main
# Resolve conflicts if any
git push --force-with-lease
```

### "I accidentally deleted a file"

```bash
git restore filename          # if not committed
git checkout HEAD filename    # restore from last commit
git checkout commit-hash -- filename  # from specific commit
```

### "I pushed sensitive information!"

```bash
# Remove from history (caution: rewrites history)
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch path/to/file" \
  --prune-empty --tag-name-filter cat -- --all

# Or use BFG Repo-Cleaner (recommended)
bfg --delete-files filename
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# Force push to remote
git push --force

# Rotate any exposed secrets immediately!
```

### "My branch has diverged from remote"

```bash
# Pull with rebase
git pull --rebase

# Or fetch and rebase manually
git fetch origin
git rebase origin/branch-name

# If you want to keep both changes
git pull --no-rebase
```

---

## GitHub-Specific Features

### GitHub Actions

Automate workflows with CI/CD:

```yaml
# .github/workflows/test.yml
name: Tests
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run tests
        run: npm test
```

### Protected Branches

Settings → Branches → Add rule:
- Require PR reviews before merging
- Require status checks to pass
- Require signed commits
- Restrict who can push

### Issue Templates

Create `.github/ISSUE_TEMPLATE/bug_report.md`:

```markdown
---
name: Bug Report
about: Report a bug
---

**Describe the bug**
A clear description

**To Reproduce**
Steps to reproduce

**Expected behavior**

**Screenshots**
```

### GitHub CLI Useful Commands

```bash
# Create a repo
gh repo create

# Create an issue
gh issue create

# View PRs
gh pr list
gh pr view number

# Check out PR locally
gh pr checkout number

# Create PR
gh pr create --title "Title" --body "Description"

# Merge PR
gh pr merge number --squash

# View run status
gh run list
gh run view
```

---

## Resources and Next Steps

### Essential Reading

- [Pro Git Book](https://git-scm.com/book/en/v2) - Comprehensive Git guide
- [GitHub Docs](https://docs.github.com) - Official documentation
- [Git Flight Rules](https://github.com/k88hudson/git-flight-rules) - Common scenarios
- [Oh Shit, Git!?!](https://ohshitgit.com/) - Fix common mistakes

### Practice Resources

- [Learn Git Branching](https://learngitbranching.js.org/) - Interactive tutorial
- [Git Immersion](http://gitimmersion.com/) - Guided tour
- [Visualizing Git](http://git-school.github.io/visualizing-git/) - Visual concepts

### Tools

- **GUIs**: GitKraken, SourceTree, GitHub Desktop, Fork
- **CLI Enhancements**: tig, lazygit, delta
- **VS Code Extensions**: GitLens, Git Graph

### Advanced Topics to Explore

- Git internals (objects, refs, packfiles)
- Large file storage (Git LFS)
- Monorepo strategies
- Git server administration
- Custom Git commands
- Advanced conflict resolution strategies

---

## Practice Checklist

Track your progress:

- [ ] Create multiple branches
- [ ] Make commits with good messages
- [ ] Merge branches (fast-forward and no-ff)
- [ ] Resolve a merge conflict
- [ ] Create and merge a pull request
- [ ] Stash and restore changes
- [ ] Use git log to explore history
- [ ] Create and push a tag
- [ ] Undo a commit with reset
- [ ] Revert a commit
- [ ] Interactive rebase to clean up history
- [ ] Cherry-pick a commit
- [ ] Set up a git hook
- [ ] Use git bisect to find a bug
- [ ] Recover a "lost" commit with reflog
- [ ] Work with a fork
- [ ] Squash commits in a PR

---

## Conclusion

Git is a powerful tool with a learning curve, but mastering it will make you a more effective developer. Start with the fundamentals, practice regularly, and gradually adopt more advanced techniques as you become comfortable.

Remember:
- 🔄 It's okay to make mistakes - Git has recovery options
- 📚 Reference this guide when you're stuck
- 🧪 Experiment in this practice repo before using techniques on real projects
- 💬 The Git community is helpful - don't hesitate to ask questions

Happy learning! 🚀

---

*This learning plan was created on October 23, 2025. Some commands or features may evolve over time. Always refer to official documentation for the most up-to-date information.*

