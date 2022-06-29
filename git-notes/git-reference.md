# Git Config
* `git config --global user.name "name"`: Set name that is put on commit messages for every repo 
  on the current user profile.
* `git config --global user.email "email"`: Set email that is put on commit messages for every repo 
  on the current user profile.
* `git config --global core.editor "/bin/vim"`: Set default editor for commit message editing.
* `git config --list`: List current editor configuration.
* Global configurations can be overridden on a per-repo basis by re-running these commands in that
  repo, sans `--global` flag.

# Git Help
* `git help <command>`: Provide in-depth manual page for the specified command.
* `git <command> -h`: Provide concise useage instructions for the specified command.

# Cloning
* `git clone <location of repo>`: Fetch remote repository information and store it locally.
    * Transfer protocols can be `https://`, `git://`, `user@server:path/git`
    * An optional target directory can be specified: `git clone <repo> local-location`

# Patching
- `git diff-files -p <filename(s)>` will generate a patch for the specified file.
- `git format-patch <commit> -o <output dir>` will generate a patch for each commit since `<commit`, **not including `<commit>` itself.** The patches are stored in `<output dir>`.
- Apply a patch with `git apply <patch name>`
- If all else fails, you can create a diff file with `git diff <first commit>..<last commit>` and apply that
  with `patch -p1 < diff_file`
    - Note that this method doesn't preserve any of the commit history, use with discretion.

# Sausage-making
- If you don't want your life to be hell, cleaning up your git history should be done on a local only branch; rewriting
  remote history sucks and is a bad idea regardless.
    - Leverage branching when working on bugs/features, i.e branch off of `development` with `feature-branch`, then branch
      off of `feature-branch` with a purely local branch, like `feature-branch-WIP`
      - Work off of `feature-branch-WIP` with whatever commits you need to make, then clean up the history when changes
        are complete, and merge them into `feature-branch`
- `git rebase -i <commit>` is useful for rewriting git commit history.
    - `<commit>` can either be a commit on the current branch, or the name of another branch; the difference in commits
      between the two branches will then be used to determine which commits will be rebased.
        - `git rebase -i feature-branch` while on `feature-branch-WIP` will allow you to edit the history of all the commits
        on `feature-branch-WIP` that aren't on `feature-branch`
    - The interactive buffer allows you to do many things to the commit history; **note that the commit at the top is the
      OLDEST commit, and the last is the NEWEST**:
        - Changing the order of the lines in the buffer will reorder the commits.
        - Removing a line will drop the commit completely.
        - Changing `pick` to `squash` will combine that commit with the one above it; you will be prompted to edit the
          commit message.
        - Changing `pick` to `fixup` will combine the commit with the one above it, but will not prompt you to change
          the commit message.
        - changing `pick` to `reword` allows you to edit the commit message for that commit.
- To replay **all** commits on the working branch to the proper feature branch:
```
git checkout <proper feature branch>
git rebase <working branch>
```

# Pruning
- To remove unused artifacts (including local branches) that are no longer needed, use `git prune`
- To remove local branches that no longer have a related upstream branch, use `git remote prune origin`

# Removing Branches
- To remove a _local_ branch, use `git branch -d <branch>`
- To remove a _remote_ branch, use `git push <remote> --delete <branch>`

# Worktrees
Worktrees are essentially branches that exist as filesystem objects; instead of using `git checkout <branch>` you would
instead `cd <dir containing worktree>`.
- `git worktree add path/to/dir --checkout <branch name>` to create a dir of an existing branch.
	- Can use `-b` to create a new branch in a directory.
- `git worktree remove` to remove a worktree; this command doesn't exist in some versions of Git for some reason.
	- An alternative workflow is using `rm` to remove the directory, then using `git worktree prune` to fully remove the
	  worktree.

# Stashes
- To show a diff of the most recent stash, use `git stash show -p`
  - To show a diff of a specific stash, use `git stash show -p <stash index>` or `git stash show -p stash@{<stash index>}`
  - Note that this output can be redirected to a text file (`git stash show -p > patch.txt`) and used as a patch.

# Misc
#### Other useful shortcuts and tools

- `git checkout -` will checkout the last branch that you were on.
	- This is short for `git checkout @{-1}`; this can also be used anywhere you would name a branch, ex. `git merge @{-1}`

- `git ls-files --error-unmatch <filename>` will check to see if a certain file is part of the files versioned by Git.
