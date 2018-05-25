# Git Notes
[The Git Book](https://git-scm.com/book/en/v2)

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