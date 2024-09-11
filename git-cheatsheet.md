# CREATE

### on local

`git init` creates new repository in current directory
`git add .` add all latest changes to the next commit

```sh
cd ~/projects/my_project
git init
git add .
```

### From existing repo

`git clone` is used to clone a repositroy from a remote.

> [!NOTE] > **Remote repositories** can be on your local machine. It is entirely possible that you can be working with a “remote” repository that is, in fact, on the same host you are. The word **“remote”** does not necessarily imply that the repository is somewhere else on the network or Internet, only that it is **elsewhere**.

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
git checkout <BRANCH>
```

### Merge branch B1 into branch B2

```sh
git checkout <B2>
git merge <B1>
```

### Create branch based on HEAD

```sh
git branch <BRANCH>
```

### Create branch based on another

```sh
git checkout <new><base>
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

# Fix the last commit

```sh
git commit -a --amend
```

# CHANGES

### Files changed in working directory

```sh
git status
```

### Changes to tracked files

```sh
git diff
```

### Changes between Ida and Ida

```sh
git diff <ID1><ID2>
```

# History of changes

```sh
git log
```

### History of changes with files changed

```sh
git whatchanged
```

### Who changed what and when in a file

```sh
git blame <file>
```

### A commit identifies by ID

```sh
git show <ID>
```

### A specific file from a specific ID

```sh
git diff <ID>:<file>
```

### All local branches

```sh
git branch # star "*" means the current branch
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

Set the default text editor used by commands for all users on the machine.

```sh
# <editor> arg should be the command that launches the desired editor (e.g. vi).
git config --system core.editor <editor>
```

Open the global configuration file in a text editor for manual editing.

```sh
git config --global --edit
```

# PGP SIGNATURE FOR COMMITS

### Set up GPG

First you have to install GPG, if you don’t already have it. `sudo apt-get install gnupg`. Verify your installation like this:

```sh
gpg --version
gpg (GnuPG) 2.2.17
libgcrypt 1.8.4
more output ...
```

If your system doesn't know them yet, you have to import your public and private keys (I assume you have them stored in files called id_ed25519 and id_ed25519.pub). If you don’t have a key pair, you can generate a new one. In that case, you can skip the import and directly jump to Set up Git. It's also possible to use your Keybase GPG key, if you have one (Stephen Rees-Carter wrote a nice article about it).

```sh
gpg --import id_ed25519 # private key
gpg --import id_ed25519.pub # public key
```

Note: when importing the private key, a GUI window appears that asks for the corresponding passphrase you set when creating your key pair.

### Set up Git

Now you can tell Git your signing key ID. It’s a 16-digit alphanumeric string that can be found with gpg --list-signatures (look for lines starting with “sig”).

```sh
git config --global user.signingkey 26A64778F76A7911
```

If you want, you can tell Git to sign commits per default (since Git 2.0), so you don’t always have to add the -s flag in the command line:

```sh
git config --global commit.gpgsign true
```

> [!NOTE]
> I use the --global flag here to apply these settings to all the repositories. Of course you can apply these settings only to the current repository without it.

### Set up GitHub

Now you have to give GitHub (or whatever Git server you’re using) your public key. You can print it with mpg --armor --export or get-content -path public.key (or open it with your favorite text editor) and copy it to your clipboard.

Now go to GitHub, click on the top right menu, go to `Settings` > `SSH and GPG keys` > New GPG key and paste your key — it should look like this:

`-----BEGIN PGP PUBLIC KEY BLOCK-----
...a lot of characters...
-----END PGP PUBLIC KEY BLOCK-----`

### Error: secret key not available

`git commit -am "commit message" -s
gpg: skipped "26A64778F76A7911": secret key not available
gpg: signing failed: secret key not available
error: gpg failed to sign the data
fatal: failed to write commit object`

**Solution**: Tell Git the full path to the GPG executable

```sh
# on Windows
git config --global gpg.program "C:\Program Files (x86)\gnupg\bin\gpg.exe"
```

# SECURITY

### Remove sensitive files from GitHub from your commit history [e.g passwords or secrets]

> [!TIP]
> for password, it might be easier to change the it if you can..

First thing you do is change the visibility of the repo. So, if it's a public repo, make it private. This way, you're sure no one else sees the file while you're working on deleting it.
Then run the following command. You have to include the path to the file and not just the file name. replacing config/secretFile.json with the path to the file you want to be removed. In my case, `secretFile.json` is inside of a folder named `config`.

```sh
git filter-branch --force --index-filter \
 "git rm --cached --ignore-unmatch config/secretFile.json" \
 --prune-empty --tag-name-filter cat -- --all
```

> [!WARNING]
> The command above deletes the file from your local repository too, so ensure you have copied the content to a notepad or whatever you use before running the command.

Then create and add your file to .gitignore so you don't accidentally push it again to GitHub. You can do that with:

```sh
echo "super-secrets-file" >> .gitignore
git add .gitignore
git commit -m 'add file to .gitignore'
```

Once you are satisfied with your changes, you can then push them to GitHub:

```sh
git push origin --force --all
And that's it! Your repo history is clean without any trace of your sensitive file.
```

# FYI

    master is the default development branch
    origin is the default upstream repository
    HEAD is the current branch
