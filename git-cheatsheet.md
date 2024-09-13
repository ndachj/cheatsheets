# Git Cheatsheet

---

# CREATE

### New repository

`git init` create a new repository in current directory.
`git add .` add all latest changes to the next commit.

```sh
cd ~/projects/project01
git init
git add .
```

### From existing repository

`git clone` clone a repositroy from a remote.

> [!NOTE]
> Remote repositories can be on your local machine. It is entirely possible that you can be working with a “remote” repository that is, in fact, on the same host you are. The word **“remote”** does not necessarily imply that the repository is somewhere else on the network or Internet, only that it is **elsewhere**.

```sh
git clone ~/existing/repo ~new/repo
git clone you@host:dir/project.git # default protocol is ssh
```

# UPDATE

### Fetch latest changes from origin

```sh
# this doesn't merge them
git fetch
```

### Pull latest changes from origin

```sh
# this does a fetch followed by a merge
git pull
```

### Apply a patch that someone sent you

```sh
git am -3 patch.mbox
git am --resolve # in case of conflict, resolve the conflict
```

# PUBLISH

### Commit all local changes

```sh
git commit -a
```

### Commit previously staged changes

```sh
git commit -m "descriptive commit message"
```

### Prepare a patch for other developers

```sh
git format-patch origin
```

### Push changes to origin

```sh
git push <origin> <branch>
```

### Make a version or a milestone

```sh
git tag <version_name>
# e.g git tag v7.0.34
```

# STASH

### Stash uncommitted changes before moving to another branch

```sh
git stash
```

### Give the stash a name for easy reference

```sh
git stash push -m
```

### Return local copy of branch back to how it was when you stashed it earlier

```sh
git stash pop
```

# BRANCH

### Switch to the BRANCH branch

```sh
git checkout <branch>
```

### Merge branch B1 into branch B2

```sh
git checkout <B2>
git merge <B1>
```

### Create branch based on HEAD

```sh
git branch <branch>
```

### Create branch based on another

```sh
git checkout <new> <base>
```

### Delete a branch

```sh
git branch -d <branch>
```

# REBASE

### Rebase with git

```sh
git debase origin/master
```

Which would apply all the changes in master, below your branch, to make your commits grouped together and more organized.

### Interactive rebase

```sh
git rebase -i HEAD~2
```

Which runs your debase in interactive mode for the most recent 2 commits (which is really helpful for squashing and renaming your commits), making it more readable.

# REVERT

For an easy revert if you just made a mistake (perhaps you forked a repo, then ended up pushing to the original instead of to a new one):

```sh
git reset --hard <commit_hash>
# e.g git reset --hard 71c27777543ccfcb0376dcdd8f6777df055ef479
```

Everything since then will be deleted once you push again. To do that, the next step would be:

```sh
git push --force
```

### Return to the last committed state

```sh
git checkout -F | git reset --hard
# you cannot easily undo a hard reset
```

### Revert the last commit

```sh
git revert HEAD # creates a new commit
```

### Revert specific commit

```sh
git revert $id # creates a new commit
```

### Fix the last commit

```sh
git commit -a --amend
```

# CHANGES

### View the changes in the working directory

```sh
git status
```

### View the changes between the working directory and the INDEX(staging area)

```sh
git diff
```

### View the changes between two arbitrary commit

```sh
git diff <commit_id1> <commit_id2>
```

### View the commits history(logs)

```sh
git log
git log  --graph --pretty=oneline --abbrev-commit --decorate # nice output
```

### View the commits history in a specific file

```sh
# same as 'git log' but show raw format diff output and skips merges
git whatchanged
```

### View who changed what and when in a file

```sh
git blame <file>
```

### View a commit identified by ID

```sh
git show <ID>
```

### View all local branches

```sh
# green asterisk "*" means the current branch
# cyan plus "+" means checked out branches
git branch
```

### Search for patterns

```sh
git grep <pattern> [path]
```

# CONFIG

Set the author name and email to be used for all commits in **current repo**.

```sh
git config user.name "Your Name"
git config user.email "your.email@example.com"
```

Set the author name and email to be used for all commits by the current user **globally**.

```sh
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

Create a shortcut or an alias for a Git command.

```sh
git config --global alias.<alias-name> <git-command>
# e.g git config --global alias.last 'log -1 HEAD --stat' # show last commit
```

Set the default text editor used by Git commands for all users on the machine.

```sh
# <editor> arg should be the command that launches the desired editor (e.g. vi).
git config --system core.editor <editor>
```

Open the global configuration file in a text editor for manual editing.

```sh
git config --global --edit
```

# SIGN COMMITS WITH SSH KEYS

[Generating a new SSH key and adding it to the ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
[configured Git to sign commits](https://docs.gitlab.com/ee/user/project/repository/signed_commits/ssh.html#configure-git-to-sign-commits-with-your-ssh-key)
Sign commits with SSH keys

# SECURITY

### Remove sensitive/personal information from GitHub in your commit history e.g passwords or secrets

> [!TIP]
> for password, it might be easier to change the it, if you can..

First, change the **repository visibility** to `private`. This way, you're sure no one else sees the file while you're working on deleting it.
Then run the following command. You have to include the path to the file and not just the file name. In my case, `secretFile.json` is inside of a folder named `config`.

```sh
git filter-branch --force --index-filter \
 "git rm --cached --ignore-unmatch config/secretFile.json" \
 --prune-empty --tag-name-filter cat -- --all
```

> [!WARNING]
> The command above deletes the file from your local repository too, so ensure you have copied the content to a notepad or whatever you use before running the command.

Then create and add your file to `.gitignore` so you don't accidentally push it again to GitHub. You can do that with:

```sh
echo "super-secrets-file" >> .gitignore
git add .gitignore
git commit -m 'add file to .gitignore'
```

Once you are satisfied with your changes, you can then push them to GitHub:

```sh
git push origin --force --all
```

And that's it! Your repo history is clean without any trace of your sensitive information.

# FYI

    origin is the default upstream repository
    HEAD is the current branch

# Credit

This cheatsheet was originally written by [Beau Williams](https://github.com/beauwilliams/Dotfiles) - I found it on his [Dotfiles](https://github.com/beauwilliams/Dotfiles/blob/master/Cheatsheets%2Fgit-cheatsheet.md), probaly serving as a quick reminder on git commands.
