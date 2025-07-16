# **GIT Guide**

Git is a distributed version control system used to track changes in code and facilitate collaboration among multiple developers. It allows you to manage versions, branch off to test changes, and synchronize code with remote repositories. This guide covers essential and advanced Git concepts to help you work efficiently in individual or team-based development environments.

---
Before we get into technical descriptions and specific conceptual knowledge lets go over some key terms
# **Git Vocabulary / Key Terms**

1. **Repository (Repo)**  
    A Git-enabled project directory that tracks all changes and version history. It can be local (on your machine) or remote (on a service like GitHub).
    
2. **Clone**  
    Creates a full local copy of a remote repository.  
    Example:  
    `git clone https://github.com/user/repo.git`
    
3. **Working Directory**  
    The actual folder and files on your machine that you're editing. Any changes you make live here before you stage or commit them.
    
4. **Staging Area (Index)**  
    A preparation space where you gather changes before making a commit. You move files here with `git add`.
    
5. **Commit**  
    Takes a snapshot of all staged changes and saves them in the repository’s history.  
    Example:  
    `git commit -m "Describe what changed"`
    
6. **Branch**  
    A parallel version of your code. Used to isolate work like features or bug fixes.  
    The main branch is usually called `main` or `master`.
    
7. **Checkout**  
    Switches your working directory to another branch or commit.  
    Example:  
    `git checkout feature-branch`
    
8. **Merge**  
    Combines the changes from one branch into another. Most commonly used to bring a feature branch into `main`.
    
9. **HEAD**  
    A pointer to the current branch or commit you're working on. It tells Git where you are in the project.
    
10. **Origin**  
    The default name Git gives to the remote repository you cloned from.  
    Example:  
    `git push origin main`
    
11. **Pull**  
    Fetches the latest changes from the remote branch and merges them into your local branch.  
    Example:  
    `git pull origin main`
    
12. **Push**  
    Sends your local commits to the remote repository.  
    Example:  
    `git push origin feature-branch`
    
13. **Remote**  
    A reference to a version of the project hosted elsewhere (typically on GitHub, GitLab, etc.).
    
14. **Fetch**  
    Downloads new data from the remote repository (like new branches or updates), but doesn’t merge anything automatically.  
    Example:  
    `git fetch origin`
    
15. **Diff**  
    Shows the difference between two versions of a file or between the working directory and the last commit.  
    Example:  
    `git diff`
    
16. **Reset**  
    Used to undo local changes. Can remove staged files or move your branch pointer.  
    Example (to unstage a file):  
    `git reset <file>`
    
17. **Revert**  
    Creates a new commit that undoes a previous one. Commonly used to undo changes in shared branches safely.
    
18. **.gitignore**  
    A file where you list files and directories you want Git to ignore. Useful for things like log files, build artifacts, or environment configs.
    
19. **Conflict**  
    Happens when Git can't automatically merge changes. You’ll need to resolve the conflict manually and commit the result.
    


# **Git Basics**

#### Configuration

Before using Git, you should configure your user information. This ensures commits are properly attributed.

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

The `--global` flag applies this configuration to all Git repositories for your user account. If needed, you can override these settings per repository by omitting the `--global` flag.

To view your current Git configuration:

```bash
git config --list
```

#### Navigation

These are standard shell commands (not Git-specific) that are commonly used when navigating to and working in Git repositories:

- `cd folder_name` – Change into a directory
    
- `cd ..` – Move up one directory
    
- `ls` – List the files and folders in the current directory
    
- `pwd` – Print the current working directory
    

---

# **Adding Material to Your Repository**

To start tracking files and changes in Git:

1. Initialize a Git repository (if not already cloned from remote):
    
    ```bash
    git init
    ```
    
2. Stage files to be tracked:
    
    ```bash
    git add .
    ```
    
    This stages all files in the current directory. You can also stage individual files with `git add <filename>`.
    
3. Check the status of your working directory:
    
    ```bash
    git status
    ```
    
4. Commit staged changes with a message:
    
    ```bash
    git commit -m "Initial commit"
    ```
    
    Committing saves the current snapshot to your local Git history.
    
5. Push commits to a remote repository:
    
    ```bash
    git push origin main
    ```
    
    Replace `origin` with your remote name (usually `origin` by default), and `main` with your current branch.
    

---

# **Branch Operations**

### Creating a Branch

Branches are isolated lines of development. They allow you to work on features or fixes without affecting the main codebase.

```bash
git checkout -b new-feature
```

This creates and switches to a new branch called `new-feature`.

After making changes and committing them, push the branch to the remote:

```bash
git push -u origin new-feature
```

### Switching Branches

```bash
git checkout main
```

Switches back to the `main` branch.

### Deleting a Branch

To delete a branch locally:

```bash
git branch -d branch-name
```

To delete a branch remotely:

```bash
git push origin --delete branch-name
```

---

# **Pulling and Merging**

To pull changes from a remote repository and merge them into your current branch:

```bash
git pull origin main
```

This fetches updates from the remote `main` branch and merges them locally.

You can also manually fetch and merge:

```bash
git fetch origin
git merge origin/main
```

To merge another local branch into your current one:

```bash
git merge feature-branch
```

If conflicts occur, Git will pause and allow you to resolve them manually before completing the merge.

---

# **Git LFS (Large File Storage)**

Git LFS is useful when working with binary or large files that don’t change frequently (e.g., media assets, datasets).

1. Install Git LFS:
    
    ```bash
    git lfs install
    ```
    
2. Track large files:
    
    ```bash
    git lfs track "*.zip"
    ```
    
3. Commit the `.gitattributes` file Git LFS creates:
    
    ```bash
    git add .gitattributes
    git commit -m "Track .zip files with LFS"
    ```
    
4. Push your changes as usual:
    
    ```bash
    git push origin main
    ```
    

LFS ensures large files are stored outside the core Git repository and linked via pointers.

---

# **Stashing Changes**

Stashing temporarily saves uncommitted changes so you can switch branches or update without losing work.

```bash
git stash
```

To apply the last stashed changes:

```bash
git stash apply
```

To view all stashes:

```bash
git stash list
```

To drop a stash after applying:

```bash
git stash drop
```

---

# **Undoing Changes**

Undo changes in various contexts:

- Discard changes in a file:
    
    ```bash
    git checkout -- filename
    ```
    
- Unstage a file:
    
    ```bash
    git reset HEAD filename
    ```
    
- Undo last commit but keep changes staged:
    
    ```bash
    git reset --soft HEAD~1
    ```
    
- Undo last commit and unstage changes:
    
    ```bash
    git reset --mixed HEAD~1
    ```
    
- Undo last commit and discard changes:
    
    ```bash
    git reset --hard HEAD~1
    ```
    

Be cautious with `--hard`, as it permanently deletes uncommitted changes.

---

# **Tagging Releases**

Tags mark specific points in Git history, typically used for versioning.

- Create a tag:
    
    ```bash
    git tag v1.0.0
    ```
    
- Push a tag to the remote:
    
    ```bash
    git push origin v1.0.0
    ```
    
- View tags:
    
    ```bash
    git tag
    ```
    

---

# **Log and History Exploration**

- View commit history:
    
    ```bash
    git log
    ```
    
- View a compact, one-line history:
    
    ```bash
    git log --oneline
    ```
    
- View commit history graph:
    
    ```bash
    git log --oneline --graph --all
    ```
    
- View changes in current working directory:
    
    ```bash
    git diff
    ```
    

---

# **Remotes**

- Add a remote:
    
    ```bash
    git remote add origin https://github.com/user/repo.git
    ```
    
- View remotes:
    
    ```bash
    git remote -v
    ```
    
- Change a remote URL:
    
    ```bash
    git remote set-url origin new_url
    ```
    

---

# **Aliases**

You can create Git aliases to speed up commands:

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm "commit -m"
```

Now you can run `git st`, `git co`, etc., for faster interactions.

---
---
# **Advanced Git Concepts**

This section covers advanced Git operations that help you rewrite history, clean up commits, and manage long-running branches effectively. These commands are powerful and can significantly impact repository history, so it's essential to understand their implications before using them in collaborative environments.

---

## **Rebase**

`git rebase` is used to move or combine a sequence of commits to a new base commit. It is often used to create a linear commit history and avoid unnecessary merge commits.

### Example: Rebasing a feature branch onto the latest `main`

```bash
git checkout feature-branch
git fetch origin
git rebase origin/main
```

This rewrites the history of `feature-branch` so that it appears as if it was created from the latest commit on `main`.

### Rebase vs Merge

- `merge` preserves history but creates a merge commit.
    
- `rebase` rewrites history for a cleaner linear log.
    

Use `rebase` when:

- Working on a personal feature branch
    
- Before opening a pull request
    
- You want to squash/reorder commits
    

Avoid `rebase` on shared branches unless everyone understands the implications.

---

## **Interactive Rebase**

Interactive rebase (`git rebase -i`) allows you to modify, reorder, squash, or delete commits.

### Example: Squashing commits interactively

```bash
git rebase -i HEAD~3
```

This opens an editor showing the last 3 commits. You can:

- `pick` — Keep commit as-is
    
- `reword` — Edit commit message
    
- `squash` — Combine with previous commit
    
- `drop` — Remove commit
    

```text
pick 123abc Add login page
squash 456def Fix typo
reword 789ghi Update error handling
```

This will combine the first two commits and allow you to edit the message for the third.

---

## **Cherry-pick**

`git cherry-pick` allows you to apply specific commits from one branch onto another.

### Example: Apply a commit from another branch

```bash
git checkout main
git cherry-pick <commit-hash>
```

This takes a single commit from another branch and applies it to your current branch.

Useful for:

- Porting a bug fix from `dev` to `main`
    
- Applying a single feature without merging the entire branch
    

---

## **Squashing Commits Before Merge**

Instead of creating a noisy pull request with dozens of commits, you can squash them into one meaningful commit.

```bash
git checkout feature-branch
git rebase -i main
```

Then squash all feature commits into one before merging into `main`.

---

## **Reset vs Revert**

### `git reset`

Used to undo local commits or staging changes.

- `--soft`: Keeps changes staged
    
- `--mixed`: Unstages changes, but keeps them in the working directory
    
- `--hard`: Removes changes from both index and working directory
    

Example:

```bash
git reset --soft HEAD~1
```

**Warning:** `--hard` deletes uncommitted changes. Use with caution.

### `git revert`

Used to create a new commit that undoes the changes of a previous commit—ideal for undoing changes in shared/public history.

```bash
git revert <commit-hash>
```

---

## **Git Reflog**

`git reflog` records updates to the tip of branches and allows recovery of commits even after reset or rebase.

```bash
git reflog
```

Each entry has a reference ID like `HEAD@{2}`. You can restore a previous state:

```bash
git reset --hard HEAD@{2}
```

This is extremely useful when you've lost commits after an incorrect `reset`, `rebase`, or `checkout`.

---

## **Fixing a Mistake After Push**

### Force push with care

If you need to rebase or reset a branch that was already pushed:

```bash
git push origin branch-name --force
```

**Use with caution** — this rewrites history on the remote and may cause conflicts for collaborators.

If collaborating with others, prefer:

```bash
git push origin branch-name --force-with-lease
```

This fails if someone else has pushed to the branch since your last pull, preventing accidental overwrite.

---

## **Cleaning Up After a Feature Branch**

1. Rebase the feature branch onto `main`:
    
    ```bash
    git checkout feature
    git rebase main
    ```
    
2. Merge into `main` using fast-forward (optional):
    
    ```bash
    git checkout main
    git merge feature
    ```
    
3. Delete the feature branch locally and remotely:
    
    ```bash
    git branch -d feature
    git push origin --delete feature
    ```
    

This helps keep your repo clean and focused.

## **Best Practices for Advanced Git Use**

- Use `rebase` for a clean, linear commit history in personal branches.
    
- Avoid rebasing shared branches unless coordinated.
    
- Use `interactive rebase` to curate commit history before pull requests.
    
- Favor `revert` over `reset` on shared branches.
    
- Use `reflog` to recover from mistakes.
    
- Prefer `force-with-lease` over `force` for safer history rewriting.
    
- Document and communicate rebase/resets with your team.
