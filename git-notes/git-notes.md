# Git Notes
Sources: 
- [The Git Book](https://git-scm.com/book/en/v2)

# Table of Contents
### [Getting Started](#getting-started)
- [Version Control](#version-control)
- [A Short History of Git](#a-short-history-of-git)
- [Basics of Git](#basics-of-git)
- [First-Time Git Setup](#first-time-git-setup)
- [Getting Help](#getting-help)

### [Git Basics](#git-basics)
- [Getting a Git Repository](#getting-a-git-repository)
- [Recording Changes to the Repository](#recording-changes-to-the-repository)
- [Viewing Commit History](#viewing-commit-history)
- [Undoing Things](#undoing-things)
- [Working with Remotes](#working-with-remotes)
- [Tagging](#tagging)
- [Git Aliases](#git-aliases)

### [Git Branching](#git-branching)
- [Branches in a Nutshell](#branches-in-a-nutshell)
- [Basic Branching and Merging](#basic-branching-and-merging)
- [Branch Management](#branch-management)
- [Branching Workflows](#branching-workflows)
- [Remote Branches](#remote-branches)
- [Rebasing(#rebasing)

## Getting Started
### Version Control
- Version control works by keeping _patch sets_ (the differences between two files) on disk; this allows a versioning system to recreate 
  the way a file looked at any given point in time.
    - An older version control system, RCS, used this method.
### A Short History of Git
- Work on the Linux kernel was shared via passing around patches and archived files.
- Eventually in 2002, the project began to use a proprietary VCS known as BitKeeper.
- The relationship with this company eventually deteriorated, and in 2005 the free-of-charge status of the software was revoked.
- This prompted the community to create its own VCS.
### Basics of Git
- Many VCSs utilize _delta-based_ version control, in which the changes made to a file over time are stored in some way.
    - __Git doesn't share this approach.__ Rather than tracking individual file changes, Git looks at the state of the entire project
      structure every time a commit is made - it is snapshot based. 
- Nearly every operation in Git is local; even the entire snapshot history of the project is stored in the local repo.
- Git checksums everything before it's stored, so files cannot be added or removed without Git being made aware.
    - Git uses SHA-1 hashes, generated off of files or directory structures.
- Most actions performed in Git only add information to the database
- Files can be in 3 states within git:
    - Committed: the data is safely stored in the local database.
    - Modified: the data has been changed but has not been committed to the database yet.
    - Staged: a modified file that has been marked to be committed in the next snapshot that's made.
- Additionally, there are 3 main secions of a Git project:
    - The Git directory: stores metadata as well as the database of snapshots.
    - The working tree: a single checkout of one version of the project. Files are pulled out of the local compressed database to the 
      disk for using or editing.
    - The staging area: a _file_ that stores information about what will go into the next commit; the technical name for this file is
      the _index_.
### First-Time Git Setup
- `git config user.name` and `git config user.email` set the name and email that is baked into the commits that are made to the current
   project.
    -  If the `--global` tag is used, that identity information will be used system-wide, on every project. To override this behavior 
       for a particular project, run the `git config` commands while inside of those directories (without the `--global` flag).
- The default editor for Git can be configured with `git config core.editor`.
- The current Git settings can be viewed with `git config --list`.
    - Keys may show up in this list more than once; this is because the keys may be read from different files and contain duplicate 
      entries.
### Getting Help
- Both `git help <command>` and `man git-<command>` provide an in-depth manual page for the specified command. 
- Quick, concise help can be accessed with the `-h` or `--help` flag after a command, i.e `git add -h`.

## Git Basics
### Getting a Git Repository
- You can either take an existing directory and turn it into a Git repo, or clone an existing one.
    - Initializing a directory: navigate to the directory and run `git init`. This creates a .git directory with the skeleton of a basic
      repository. 
        - The default branch, _master_, is created as part of this process.
        - At this point, none of the files will be tracked; an initial commit should be made to add them to the database.
    - Cloning: to copy the entirety of an existing repo (which is normal for Git), use `git clone <url or filepath>`.
        - Optionally, a target directory can be specified, i.e. `git clone www.somerepo.com/foo local-dir`.
        - Different transfer protocols can be used, such as `https://`, `git://`, or `user@server:path/to/repo.git` (SSH).
### Recording Changes to the Repository
- Files can be tracked or untracked; tracked files indicate that they were present in the last snapshot, whereas untracked were not.
- Modified tracked files and untracked files can be viewed wth `git status` (or, in a more concise way, `git status -s`).
    - For a more concise breakdown of exactly what has been changed, use `git diff`.
        - No arguments will show a diff of unstaged changes; to compare staged files to the previous commit, use `git diff --staged` 
          (synonymous with `--cached`).
        - This is important to remember, because __if all changes are staged `git diff` by itself will show no output__.
        - Diffs can also be viewed with an external tool by running `git difftool`.
- When a file is staged, it's done so with its current state in that moment, and won't change if the file is modified. That means that 
  if a file is staged, then modified, the old changes will be staged but the new ones will not. 
        - `git add` will have to be run again to stage the new changes.
- Files can be ignored by adding them to the `.gitingore` file.
    - GitHub maintains a list of good gitignore file examples [here](https://github.com/github/gitignore).
    - Gitignore files may include the following delarations:
```cpp
// This can be all of one type of file:
*.o

// Explicit declarations of files that you do want, which might be ignored otherwise:
!lib.o

// Entire directories:
build/

// A specific file:
/foo/TODO

// A regular expression:
*[0-9]*.log
// Files in a specific directory as well as in all of its subdirectories:
doc/**/*.o
```
- Commits are made with `git commit`. This will open the configured default editor so that a commit message can be written.
    - `-m` allows a commit message to be written from the command line.
    - `-v`will put diff information in the editor so that you can see exactly what is changed (this does nothing if the `-m` flag is
      also used).
    - `-a` will include all tracked files that have changed, and is equivalent to `git add *; git commit`. This skips the staging 
      process entirely.
- Removed files must be removed from the tracked files and then committed. `git rm` will do this, as well as remove the file locally.
    - If a file is simply removed, it will show up as "Changes not staged for commit" when `git status` is called - the file was not
      removed from the tracked files. Use `git rm path/to/file` to stage the deletion and have the file removed from the tracked files.
    - Note that if a file is staged for commit, it cannot be removed without using the `-f` flag; this is a safety mechanism.
    - If you want to keep a local copy of a file, but no longer want Git to track it, you can use `git rm --cached <file>`; this will
      not delete the local copy of the file but it will no longer be tracked by Git.
    - Globbing patterns can also be passed to `git rm`, i.e. `git rm log/\*.log`. Note that the Kleene star _must_ be escaped with a 
      backslash, as Git does its own filename expansion in addition to any that your shell performs. 
- Files can be moved via Git as well: `git mv oldname newname`.
    - Git does not explicitly track file movement, but it's capable of figuring it out on its own after the fact. Thus `git mv f1 f2`
      is equivalent to:
    ```
    mv f1 f2
    git rm f1
    git add f2
    ```
    - In either case Git will report a file rename in `git status`.
### Viewing Commit History
- Use `git log` to view commit history of a repo, newest to oldest.
    - Using the `-p` or `--patch` flag will show the differences introduced in each commit.
        - The `--stat` flag performs a similar function, but provides a brief summary of changes rather than a diff.
    - Specifying a number as a flag argument, such as `-2`, will show that many commits in the output.
    - The `--pretty=<option>` flag formats the output in some way:
        - `--pretty=oneline` puts each commit on one line, so more can be viewed at once.
        - `--pretty=[short | full | fuller]` provide slightly different amounts of information than normal `git log`.
        - `--pretty=format:"<format string>"` allows for user-defined formatting, with a variety of format specifiers, including:

        | Specifier | Description |
        |-------|-------|
        | `%H` | Commit hash |
        | `%an` | Author name |
        | `%s` | Commit message |

        Other specifiers can be found [here](https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History).
    - The `--graph` flag provides an ASCII chart of the branch and merge history; works well with `oneline` and custom formats.
    - `--since` and `--until` both take date formats and limit the output based on them. `--since=2.weeks`, `--until=2008-1-1`, etc are
      some of the valid options.
    - The `-S <string>` flag finds commits that added or removed occurances of said string and displays only those.
    - The `--decorate` flag will show what commit branches are currently pointing to.
    - A path can be passed to log command as well, which will only show changes made to that specific file or files within that directory.
### Undoing Things
- If you commit files to early, and there are additional changes that you would like to include in your commit, stage those files and 
  use `git commit --amend`. The amended commit will replace the previous one.
- Files can be unstaged with `git reset HEAD <file>`. The file will retain its modifications but will no longer be staged.
- To discard all changes made since your last commit, use `git checkout -- <file>`.
### Working with Remotes
- `git remote` will list the remote servers that have been configured for the current repository.
    - `-v` will display the URLs for the remote(s) as well.
    - Additional information can be provided by using `git remote show`. This will grab information from the remote branch and display it.
- `git clone` will implicitly add the `origin` remote for you, but others can be specified manually with `git remote add <shortname> <URL>`.
    - `git remote rename <old name> <new name>` will let you change the shortname of an existing remote.
    - `git remote remove <name>` will remove a remote.
- Shortnames can be used instead of entire URLs when running commands, i.e. `git fetch SomeRemote`.
    - The master branch of SomeRemote is now available as SomeRemote/master.
- `git fetch` will go out and grab any data in the remote branch and store it locally; __this does not merge any of the changes into your
   current working state.__ If no shortname is specified, `origin` is used implicitly.
    - Changes can be merged manually with `git merge FETCH_HEAD`.
- `git pull` performs a fetch, but will also automatically merge any remote changes into the local state.
- Changes are pushed with `git push <remote> <branch name>`. If any pushes have been made since you pulled from the remote, your push 
  will be rejected - the remote changes must be pulled and merged first.
### Tagging
- Tags can be listed with `list tags`.
    - Pattern matching is supported as well, i.e. `list tags -l "v1.8.*"`.
- There are two types of tags, _lightweight_ and _annotated_.
    - Lightweight: functions like a branch that doesn't change; a lightweight tag is essentially a pointer to a specific commit.
        - Created as so: `git tag <name of tag>`, no flags needed.
    - Annotated: stored as full objects in the database, and they contain information on the tagger, the date the tag was made, etc.
        - Created with the `-a` flag: `git tag -a "v1.0" -m "Tagging message"`.
        - Information on these tags can be displayed with `git show <tag name>`.
        - This is the recommended method for creating permanent tags.
- Tags can be applied in earlier commits as well, by providing the commit checksum (or as much as is necessary to uniquely identfy it),
  i.e. `git tag -a "v1.0" 9fceb02`.
- __Tags are not automatically pushed to remotes.__ They must be pushed manually with `git push <remote> <tag name>`.
    - Alternatively, `git push <remote> --tags` will push all local tags that are not part of the remote repo.
- Tags can be checked out, i.e. `git checkout <name of tag>`, but this creates side effects.
    - This puts the local repository in the "detached HEAD" state, meaning that any commits that are made will not be part of any branch,
      and will be unreachable except by using its specific hash. 
    - It's a better practice to create a new branch from the tagged commit, make changes there, then merge it back in.
### Git Aliases
- Aliases to Git commands can be created with `git config`, i.e. `git config --global alias.co checkout` will allow you to type `git co`
  instead of `git checkout`.
- Tags can be included as well: `git config --global alias.unstage 'reset HEAD --'`.
    - With this alias, `git unstage File1` and `git reset HEAD -- File1` are equivalent.
- External commands can be run as well, by using "!": `git config --global alias.visual '!gitk'`.

## Git Branching
### Branches in a Nutshell
- Git stores its data as a series of snapshots; specifically, commits are stored as commit objects, which contain a pointer to the 
  snapshot containing the content that was staged, as well as author information, commit message, and pointer(s) to parent commit(s).
    - It's possible for a commit to have 0 parents (initial commit), 1 parent (normal commit), or many parents (when two or more 
      branches are merged).
- Several objects are created as changes are staged and committed:
    - When a change is staged, Git computes a checksum for each committed file, adds that checksum to the staging area, and stores that 
      version of the file in the repository (as what's known as a _blob_).
    - When a commit is created, Git calculates a checksum for each directory, and stores those tree objects in the repository. These 
      tree objects contain the checksums of each file/subdirectory blob contained within it.
    - A commit object is created, which contains the author, date, commit message, and a pointer to the root tree blob that was
      previously created.
        - A commit object will also include a pointer to the previous commit, if there is one.
- A branch is nothing more than a pointer to one of these commit objects.
    - When a new branch is created, a new pointer to the current commit is created.
        - Git tracks the commit you're currently on via the HEAD pointer. __This is a pointer exactly like branch pointers, but it always
          points to the current commit.__
        - The location of each branch can be printed with `git log --decorate`.
- A branch can be created with `git branch <branch name>`; this branch is not switched to automatically.
    - On disk this is nothing more than the 40 character SHA-1 hash of the commit that the branch points to (typically the most recent
      commit in the branch).
- Delete a branch with `git branch -d <branch to delete>`.
- Use `git checkout <branch name>` to change the currently active branch.
### Basic Branching and Merging
- Use `git merge <branch to merge into current>` to merge changes from one branch to another.
    - If the target branch is directly ahead of the current branch in terms of commit history (AKA there were no divergent changes and
      the current branch can be directly traced to in the commit history), a fast-forward will occur.
        - A fast-forward simply takes the current branch pointer and sets it equal to the target branch pointer, because there is no
          divergent work that needs to be resolved.
    - If the target branch is _not_ a direct ancestor of the current branch, a simple fast-forward won't work. Instead, Git merges the 
      branches together, using the snapshot to merge into (the current branch), the snapshot to merge in (the target branch), and the
      most recent common ancestor that the two share.
        - Because this is more complex than a fast-forward, Git will create a new commit and automatically push it when a merge is 
          performed. 
        - Merge conflicts will arise when the two branch snapshots have files that both have modified; Git cannot resolve this cleanly
          and will leave it up to you to fix the issues before finalizing the merge commit.
            - When this happens, Git will automatically put conflict-resolution markers in your files:
            ```
            <<<<<<< HEAD
            foo
            =======
            bar
            >>>>>>> TargetBranch
            ```
            The lines between `<<<<<<< HEAD` and `=======` are changes present in the current branch, while the lines between
            `=======` and `>>>>>>>` are the changes present in the target branch that is trying to be merged in. You can then go through
            and resolve each conflict manually, removing the markers in the process.

            - Once these conflicts are resolved, they can be staged with `git add` and committed.
### Branch Management
- `git branch` with no arguments will list of all current branches.
  - The "*" will indicate which branch is currently checked out (AKA where HEAD is pointing).
- `git branch -v` will show the most recent commit made to each branch.
- The `--merged` and `--not-merged` flags will filter out branches you have or have not merged into your current branch, respectively.
  - Typically, branches that are filtered in with `--merged` are generally safe to delete, because their work has been merged into another
    branch already.
    - If you attempt to delete a branch that has not been merged into another, Git will say that the branch has not been fully merged, 
      and will not delete the branch (deletion can be forced with the `-D` flag).
### Branching Workflows
- Long-running branches: a typical workflow within Git has stable code residing on some master branch, while new features/releases may be 
  separate long-lived branches that are never deleted; ideally, the older commits contain more stable code, while the newer ones contain 
  potentially more volatile changes.
- Topic branches: a short-lived branch that is used for a particular task or fix. Since branches cost almost nothing to create and 
  destroy, there's no real overhead in branching for even small changes.
### Remote Branches
- Remote references are references/pointers that are stored in your remote repos - branches, tags, etc.
  - Can be displayed with `git ls-remote <remote>` or `git remote show <remote>`.
- More commonly, remote-tracking branches are used to keep tabs on the remote repo state, which are updated every time a connection is 
  made to the remote repo.
    - These branches cannot be moved locally, and are represented as `<remote>/<branch>`.
    - When a remote repo is cloned, for example, that remote is automatically named `origin`, its master branch is cloned into the local
      repo, and creates a pointer named `origin/master` to where the remote master branch HEAD is; another branch is created, simply 
      called `master`, and points it to the same commit. __These are two distinct branches.__
- `git fetch <remote>` will update the remote-tracking branches in the local repo.
- Local branches must be explicitly pushed to a remote to synchronize them. You can do this via `git push <remote> <branch>`.
  - By doing so, anyone else who connects to the remote from that point forward will also receive a remote-tracking branch for your 
    branch.
    - Note that __having a remote-tracking branch does not necessarily mean you've pulled local copies of that branch's files.__
- Remote-tracking branches can be merged into your current branch by using their qualified names: `git merge origin/somebranch`.
- Tracking branches are created when a remote branch is checked out in a local repository; these branches automatically track a remote
  that is referred to as its _upstream branch_.
  - Tracking branches can be explicitly created by checking out a remote branch: `git checkout -b somebranch origin/somebranch`; the 
  `--track` flag does this as well: `git --track origin/somebranch`.
    - Additionally, Git will immplicitly create a tracking branch if you attempt to checkout a branch that (a) doesn't exist locally and
      (b) maches the exact name of a remote branch in only one remote.
    - A tracking branch can have a different local branch name than its upstream branch; the 
      syntax `git checkout -b localbranch origin/somebranch` is perfectly valid.
  - Using `git pull` on a tracking branch without specifying remote and branch is okay, because the branch implicitly knows what its
    upstream branch is.
  - `git branch -vv` will list any tracking branches in the project, as well as their remote upstream branch.
  - To delete a remote branch, use `git push --delete somebranch`.
### Rebasing
- Rebasing provides a similar function to merge, but is not exactly the same.
  - A merge takes two recent branch snapshots, their most recent common ancestor, and performs a three-way merge resulting in a new 
    snapshot.
  - Rebasing takes all of the changes that were performed on the current branch and replays them on top of another:
    `git rebase <target branch>`.
- The rebasing process is as follows:
  1. Git goes to the most recent common ancestor of the two snapshots.
  2. For each commit created between that ancestor and the current snapshot (the one being rebased), the diff is acquired and saved to a
     temporary file.
  3. The current branch is reset to the same commit as the target branch.
  4. The diffs are applied to that branch one at a time.