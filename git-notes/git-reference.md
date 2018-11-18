### [Git Config](#git-config)
### [Git Help](#git-help)
### [Cloning](#cloning)

---

## Git Config
* `git config --global user.name "name"`: Set name that is put on commit messages for every repo 
  on the current user profile.
* `git config --global user.email "email"`: Set email that is put on commit messages for every repo 
  on the current user profile.
* `git config --global core.editor "/bin/vim"`: Set default editor for commit message editing.
* `git config --list`: List current editor configuration.
* Global configurations can be overridden on a per-repo basis by re-running these commands in that
  repo, sans `--global` flag.

## Git Help
* `git help <command>`: Provide in-depth manual page for the specified command.
* `git <command> -h`: Provide concise useage instructions for the specified command.

## Cloning
* `git clone <location of repo>`: Fetch remote repository information and store it locally.
    * Transfer protocols can be `https://`, `git://`, `user@server:path/git`
    * An optional target directory can be specified: `git clone <repo> local-location`

## Patching
- `git diff-files -p <filename(s)>` will generate a patch for the specified file.
- `git format-patch <commit> -o <output dir>` will generate a patch for each commit since `<commit`, **not including `<commit>` itself.** The patches are stored in `<output dir>`.
- Apply a patch with `git apply <patch name>`
