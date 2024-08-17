# **GIT Guide**

Git is a distributed version control system that allows multiple users to collaborate on projects efficiently. It tracks changes in files, facilitates teamwork, and ensures version control. Here's a comprehensive guide to using Git for managing your projects effectively.


---

# **Git Basics**

#### Configuration:
Before you start using Git, configure your identity with the following commands:

1. `git config --global user.name "Your Username"`: Set your Git username.
2. `git config --global user.email "Your Email"`: Set your Git email. This email is used to identify your contributions.

#### Navigation:
In Git, you can navigate directories using the following commands:

- `cd 'folder_name'`: Change directory. Use single quotes for folder names with spaces.
- `ls`: List the contents of the current directory.
- `pwd`: Print the current working directory path.


---

# **Adding material to your repository:**

To add material to your Git repository, follow these steps:

1. Initialize a Git repository in the local directory (not necessary if cloning an existing repository).
   ```bash
   git init
   ```

2. Add all files in the current directory to the staging area.
   ```bash
   git add .
   ```

3. Check the status of the files, which shows the changes to be committed.
   ```bash
   git status
   ```

4. Commit the added files with a descriptive message.
   ```bash
   git commit -m "Your commit message here"
   ```

   - Note: Committing saves changes to the local repository, different from pushing, which updates the remote repository.

5. Push the committed code to the remote repository.
   ```bash
   git push "https://repo.url"
   ```

   - Replace `origin` with the remote repository name and `master` with the branch name.



---

# **Branch operations:**

### Creating a Branch:

Creating a branch allows for isolated development within a repository:

1. Create and switch to a new branch simultaneously.
   ```bash
   git checkout -b NewBranch
   ```

2. Make changes to your local repository and commit them to the new branch.

3. Push the changes to your new branch in the remote repository.

4. Your changes are now reflected in the new branch of the repository.

### Deleting a Branch:

To delete a branch in Git:

1. Switch to the branch you want to delete.
   ```bash
   git checkout <branch_name>
   ```

2. Delete the branch locally.
   ```bash
   git branch -d <branch_name>
   ```

3. Delete the branch remotely.
   ```bash
   git push origin --delete <branch_name>
   ```



---

# **Advanced GIT concepts:**

##### Notes on Git LFS (Large File Storage):
Git LFS is an extension for handling large files with Git. To use Git LFS:
1. Install Git LFS on your system.
   ```bash
   git lfs install
   ```

2. Track large files using Git LFS.
   ```bash
   git lfs track "*.extension"
   ```
   - Replace `"*.extension"` with the file extensions you want to track.
3. Commit and push changes as usual. Large files will be stored in Git LFS.

... in progress



---

 #  **Pulling / Merging**

```
git pull origin main 
```

^Fetches upstream changes and merges them into your local codebase